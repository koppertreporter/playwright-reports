# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: lagBuster.spec.ts >> Planner - Lag Buster >> TC:14339 - Verify modal does NOT appear when clients work on DIFFERENT locations
- Location: playwright/specs/lagBuster.spec.ts:424:7

# Error details

```
Error: expect(locator).toBeEnabled() failed

Locator: locator('section > div').filter({ hasText: 'Plkw P. van Geest B.V. - Loc. Gerbera' }).getByRole('button', { name: /^Order$/i })
Expected: enabled
Timeout: 30000ms
Error: element(s) not found

Call log:
  - Expect "toBeEnabled" with timeout 30000ms
  - waiting for locator('section > div').filter({ hasText: 'Plkw P. van Geest B.V. - Loc. Gerbera' }).getByRole('button', { name: /^Order$/i })

```

```yaml
- text: You're on DEVELOPMENT OR STAGE
- img
- menu:
  - menuitem "Order":
    - link "Order":
      - /url: /apps/order
  - menuitem "Knowledge":
    - link "Knowledge":
      - /url: /apps/protect
  - menuitem "Scout":
    - link "Scout":
      - /url: https://www.natutecscout.com/apps/scout
  - menuitem "Organization":
    - link "Organization":
      - /url: /apps/organization
- text: Plkw P. van Geest Maasland B.V.
- img "user"
- text: Playwright DudeWunzxOq
- complementary:
  - menu:
    - menuitem "Planner":
      - img
      - link "Planner":
        - /url: /apps/order/planner
    - menuitem "Order list":
      - img
      - link "Order list":
        - /url: /apps/order/order-list
  - img "left"
- main:
  - heading "Planner" [level=2]
  - separator
  - text: Locations 2/7 selected
  - combobox
  - switch [checked]
  - text: Order mode
  - textbox "Search..."
  - img
  - text: "Week selection:"
  - textbox "week 41 - 2026":
    - /placeholder: Start week
  - img "calendar"
  - text: "-"
  - textbox "week 52 - 2026":
    - /placeholder: End week
  - img "calendar"
  - text: Plkw P. van Geest B.V. - Loc. Gerbera No order data + Add product Plkw P. van Geest Maasland bio Sierteelt No order data + Add product 1 location has unsaved changes
  - button "Order all"
  - text: All rights reserved. © Koppert 2026
  - button "Digital Assistant":
    - img
    - text: Digital Assistant
```

# Test source

```ts
  744 |     await block.scrollIntoViewIfNeeded();
  745 |     if (await isSeeded()) return;
  746 | 
  747 |     // Rows added through the modal are client-side only until an order is placed, and the
  748 |     // planner's live-sync poll drops them again. Re-seed until they survive a sync tick.
  749 |     for (let attempt = 0; attempt < 3; attempt++) {
  750 |       if (!(await isSeeded())) {
  751 |         await this.addProductsWithoutLeadTimeToLocation(
  752 |           page,
  753 |           locationName,
  754 |           Math.max(1, count - (await orderableProductCount())),
  755 |         );
  756 |       }
  757 | 
  758 |       await this.settleLiveSync(page);
  759 | 
  760 |       if (await isSeeded()) {
  761 |         await expect(planner.weekHeaders.first()).toBeVisible({ timeout: 30000 });
  762 |         return;
  763 |       }
  764 |     }
  765 | 
  766 |     throw new Error(`Location "${locationName}" still has fewer than ${count} orderable product(s) after re-seeding`);
  767 |   },
  768 | 
  769 |   // addProductsWithoutLeadTimeFromModal through the "+ Add product" button of one location's block.
  770 |   async addProductsWithoutLeadTimeToLocation(page: Page, locationName: string, count = 1) {
  771 |     const planner = plannerSelectors(page);
  772 |     const block = this.locationBlock(page, locationName);
  773 | 
  774 |     const addButton = block
  775 |       .locator('[data-testid="add-product"]')
  776 |       .or(block.getByText('+ Add product'))
  777 |       .first();
  778 |     await addButton.scrollIntoViewIfNeeded();
  779 |     await addButton.click();
  780 |     await expect(planner.addProductModal).toBeVisible({ timeout: 10000 });
  781 | 
  782 |     const checkboxes = planner.addProductModalCheckboxes;
  783 |     await expect(checkboxes.first()).toBeVisible({ timeout: 15000 });
  784 | 
  785 |     let selected = await this.selectProductsWithoutLeadTime(page, count);
  786 | 
  787 |     const available = await checkboxes.count();
  788 |     for (let i = 0; i < available && selected < count; i++) {
  789 |       const checkbox = checkboxes.nth(i);
  790 |       const isEnabled = await checkbox.isEnabled().catch(() => false);
  791 |       const isChecked = await checkbox.isChecked().catch(() => false);
  792 |       if (!isEnabled || isChecked) continue;
  793 |       await checkbox.check();
  794 |       selected++;
  795 |     }
  796 | 
  797 |     if (selected === 0) {
  798 |       await this.closeAddProductModalByCancel(page);
  799 |       return;
  800 |     }
  801 | 
  802 |     await expect(planner.addProductModalAddButton).toBeEnabled({ timeout: 10000 });
  803 |     await planner.addProductModalAddButton.click();
  804 |     await expect(planner.addProductModal).toBeHidden({ timeout: 10000 });
  805 | 
  806 |     // "No order data" also renders `row:` ids, so wait for the added product's cells instead.
  807 |     await expect(block.locator('input[data-testid^="orderable-cell:"]').first()).toBeVisible({ timeout: 30000 });
  808 |   },
  809 | 
  810 |   // Like seedSyncedOrderViaUI, but inside one location's block. A cell's test id carries no location, so
  811 |   // when two locations hold the same product a page-wide lookup by test id matches both.
  812 |   async seedSyncedOrderInLocationViaUI(
  813 |     page: Page,
  814 |     locationName: string,
  815 |     quantity = '10',
  816 |     timeoutMs = 90000,
  817 |   ): Promise<{ testId: string; value: string }> {
  818 |     const planner = plannerSelectors(page);
  819 |     const block = this.locationBlock(page, locationName);
  820 |     const inputs = block.locator('input[data-testid^="orderable-cell:"]:not([readonly]):not([disabled])');
  821 | 
  822 |     let targetTestId = '';
  823 |     const count = await inputs.count();
  824 |     for (let i = 0; i < count; i++) {
  825 |       const input = inputs.nth(i);
  826 |       if (!(await input.isEditable().catch(() => false))) continue;
  827 |       const value = (await input.inputValue()).trim();
  828 |       if (value && value !== '0') continue;
  829 |       targetTestId = (await input.getAttribute('data-testid')) || '';
  830 |       if (targetTestId) break;
  831 |     }
  832 |     if (!targetTestId) throw new Error(`No editable empty orderable cell in "${locationName}" to seed an order`);
  833 | 
  834 |     const sync = this.trackOwnOrderSync(page);
  835 |     try {
  836 |       const target = block.locator(`input[data-testid="${targetTestId}"]`);
  837 |       await target.scrollIntoViewIfNeeded();
  838 |       await target.click();
  839 |       await target.fill(quantity);
  840 |       await page.keyboard.press('Tab');
  841 |       await page.waitForTimeout(500);
  842 | 
  843 |       const orderButton = block.getByRole('button', { name: /^Order$/i });
> 844 |       await expect(orderButton).toBeEnabled({ timeout: 30000 });
      |                                 ^ Error: expect(locator).toBeEnabled() failed
  845 |       await orderButton.click();
  846 |       if (await this.handleOrderReferencesMandatoryIfNeeded(page)) await orderButton.click();
  847 |       if (await planner.orderConfirmationModal.isVisible({ timeout: 5000 }).catch(() => false)) {
  848 |         await planner.orderConfirmationModalConfirmButton.click();
  849 |         await expect(planner.orderConfirmationModal).toBeHidden({ timeout: 10000 });
  850 |       }
  851 |       await page.waitForTimeout(3000);
  852 |       await this.dismissPostOrderModals(page);
  853 | 
  854 |       // A fresh user can bounce to Home once after their first order - see seedEditableOrderViaUI.
  855 |       if (!page.url().includes('/apps/order/planner')) {
  856 |         await this.gotoPlannerStable(page);
  857 |         await this.switchOrderMode(page, true);
  858 |         await expect(planner.weekHeaders.first()).toBeVisible({ timeout: 30000 });
  859 |       }
  860 | 
  861 |       await sync.waitForSync(timeoutMs);
  862 |     } finally {
  863 |       sync.stop();
  864 |     }
  865 | 
  866 |     return { testId: targetTestId, value: quantity };
  867 |   },
  868 | 
  869 |   // Like addProductsFromModal, but prefers products with no lead time. The modal lists the catalog
  870 |   // alphabetically, so "the first unchecked product" is the same product every time (Addit/2,5l/NL),
  871 |   // and whether it has any orderable cell depends on its lead time on the day, which F&O changes.
  872 |   // Falls back to the first unchecked products when no such product can be picked.
  873 |   async addProductsWithoutLeadTimeFromModal(page: Page, count = 1) {
  874 |     const planner = plannerSelectors(page);
  875 | 
  876 |     await this.openAddProductModal(page);
  877 | 
  878 |     const checkboxes = planner.addProductModalCheckboxes;
  879 |     await expect(checkboxes.first()).toBeVisible({ timeout: 15000 });
  880 | 
  881 |     let selected = await this.selectProductsWithoutLeadTime(page, count);
  882 | 
  883 |     const available = await checkboxes.count();
  884 |     for (let i = 0; i < available && selected < count; i++) {
  885 |       const checkbox = checkboxes.nth(i);
  886 |       const isEnabled = await checkbox.isEnabled().catch(() => false);
  887 |       const isChecked = await checkbox.isChecked().catch(() => false);
  888 |       if (!isEnabled || isChecked) continue;
  889 |       await checkbox.check();
  890 |       selected++;
  891 |     }
  892 | 
  893 |     if (selected === 0) {
  894 |       await this.closeAddProductModalByCancel(page);
  895 |       return;
  896 |     }
  897 | 
  898 |     await expect(planner.addProductModalAddButton).toBeEnabled({ timeout: 10000 });
  899 |     await planner.addProductModalAddButton.click();
  900 |     await expect(planner.addProductModal).toBeHidden({ timeout: 10000 });
  901 | 
  902 |     await expect(planner.productRows.first()).toBeVisible({ timeout: 30000 });
  903 |   },
  904 | 
  905 |   // Ticks up to `count` products with no lead time in the open Add product modal. They are looked up in
  906 |   // the catalog the page itself loads (with the page's own token) and picked by id through the modal
  907 |   // search - selections survive the search filter. Returns how many were selected: 0 when the catalog
  908 |   // cannot be read, and the caller then falls back to the first ones.
  909 |   async selectProductsWithoutLeadTime(page: Page, count: number): Promise<number> {
  910 |     const planner = plannerSelectors(page);
  911 | 
  912 |     const session = await page
  913 |       .evaluate(() => {
  914 |         const auth = JSON.parse(localStorage.getItem('stores-portal/auth') || '{}')?.state?.auth;
  915 |         const enterprise = JSON.parse(localStorage.getItem('stores-portal/enterprise-selector') || '{}')?.state;
  916 |         return {
  917 |           token: auth?.accessToken as string | undefined,
  918 |           enterpriseId: enterprise?.currentEnterpriseId as string | undefined,
  919 |         };
  920 |       })
  921 |       .catch(() => ({ token: undefined, enterpriseId: undefined }));
  922 |     if (!session.token || !session.enterpriseId) return 0;
  923 | 
  924 |     const response = await page.request
  925 |       .get(`${process.env.apiURL}/api/v1/ordering/products/enterprise`, {
  926 |         // commonHeaders carries the Referer stage requires: without it the API redirects to the web app.
  927 |         headers: { ...commonHeaders(session.token), 'x-enterprise-id': session.enterpriseId },
  928 |       })
  929 |       .catch(() => null);
  930 |     if (!response?.ok()) return 0;
  931 | 
  932 |     const products: { id: string; isActive?: boolean; isVisible?: boolean; leadTimeDays?: number[] | null }[] =
  933 |       (await response.json().catch(() => null))?.items ?? [];
  934 |     const candidates = products.filter(
  935 |       (product) => product.isActive && product.isVisible && !product.leadTimeDays?.some((days) => days > 0),
  936 |     );
  937 | 
  938 |     let selected = 0;
  939 |     for (const product of candidates.slice(0, count + 20)) {
  940 |       if (selected >= count) break;
  941 |       await planner.addProductModalSearchInput.fill(product.id);
  942 |       // The search is debounced: until it applies, the full list is still on screen and re-renders under
  943 |       // the click, which drops the tick. Wait for the list to narrow down before touching a checkbox.
  944 |       await expect
```