# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: planner_4.spec.ts >> Planner - Add Product Modal >> TC:17299 - [Cancel order popup] Verify confirmation popup before canceling order line on planner
- Location: playwright/specs/planner_4.spec.ts:975:7

# Error details

```
Error: expect(locator).toBeEnabled() failed

Locator: getByRole('button', { name: /^Order$/i }).first()
Expected: enabled
Timeout: 30000ms
Error: element(s) not found

Call log:
  - Expect "toBeEnabled" with timeout 30000ms
  - waiting for getByRole('button', { name: /^Order$/i }).first()

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
- text: Playwright DudeBoMHyRd
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
  - text: Locations Plkw P. van Geest Maasland Groenten paprika
  - combobox
  - switch [checked]
  - text: Order mode
  - textbox "Search..."
  - img
  - text: "Week selection:"
  - textbox "week 42 - 2026":
    - /placeholder: Start week
  - img "calendar"
  - text: "-"
  - textbox "week 53 - 2026":
    - /placeholder: End week
  - img "calendar"
  - img
  - text: Plkw P. van Geest Maasland Groenten paprika week 53 Mon 28-12 Tue 29-12 Wed 30-12 Thu 31-12 week 52 Mon 21-12 Tue 22-12 Wed 23-12 Thu 24-12 week 51 Mon 14-12 Tue 15-12 Wed 16-12 Thu 17-12 Fri 18-12 week 50 Mon 07-12
  - img
  - text: Tue 08-12 Wed 09-12 Thu 10-12 Fri 11-12 week 49 Mon 30-11 Tue 01-12 Wed 02-12 Thu 03-12 Fri 04-12 week 48 Fri 27-11 week 47 week 46 week 45 week 44 week 43 week 42
  - img
  - text: Anso-Mite/125,000/6l + Add product
  - textbox: "10"
  - textbox
  - textbox
  - textbox
  - textbox
  - textbox
  - textbox
  - textbox
  - textbox
  - textbox
  - textbox
  - text: — — — — — — All rights reserved. © Koppert 2026
  - button "Digital Assistant":
    - img
    - text: Digital Assistant
```

# Test source

```ts
  913  | 
  914  |     let inactiveProductCheckboxA = null;
  915  |     const countA = await productCheckboxesA.count();
  916  |     for (let i = 0; i < countA; i++) {
  917  |       const checkbox = productCheckboxesA.nth(i);
  918  |       const value = await checkbox.getAttribute('value');
  919  |       if (value === inactiveProductValue) {
  920  |         inactiveProductCheckboxA = checkbox;
  921  |         break;
  922  |       }
  923  |     }
  924  | 
  925  |     if (inactiveProductCheckboxA) {
  926  |       await expect(inactiveProductCheckboxA).toBeChecked({ timeout: 10000 });
  927  |       await expect(inactiveProductCheckboxA).toBeDisabled({ timeout: 10000 });
  928  |     }
  929  | 
  930  |     await planner.addProductModalCloseIcon.click();
  931  |     await expect(planner.addProductModal).toBeHidden({ timeout: 10000 });
  932  | 
  933  |     await planner.locationsDropdown.click();
  934  |     await expect(planner.locationsDropdownPanel).toBeVisible({ timeout: 10000 });
  935  | 
  936  |     const locationAOptionAgain = planner.locationOptionByName(locationAName);
  937  |     await locationAOptionAgain.waitFor({ state: 'visible', timeout: 10000 });
  938  |     const isAChecked = await isSelectOptionChecked(locationAOptionAgain);
  939  |     if (isAChecked) {
  940  |       await locationAOptionAgain.click({ force: true });
  941  |       await expectSelectOptionChecked(locationAOptionAgain, false);
  942  |     }
  943  | 
  944  |     const locationBOption = planner.locationOptionByName(locationBName);
  945  |     await locationBOption.waitFor({ state: 'visible', timeout: 10000 });
  946  |     const isBChecked = await isSelectOptionChecked(locationBOption);
  947  |     if (!isBChecked) {
  948  |       await locationBOption.click({ force: true });
  949  |       await expect(selectOptionTick(locationBOption)).toBeVisible({ timeout: 10000 });
  950  |     }
  951  | 
  952  |     await page.keyboard.press('Escape');
  953  |     await expect(planner.locationsDropdownPanel).toBeHidden({ timeout: 10000 });
  954  |     await expect(planner.weekHeaders.first()).toBeVisible({ timeout: 30000 });
  955  | 
  956  |     await plannerPage.openAddProductModal(page);
  957  | 
  958  |     const productCheckboxesB = planner.addProductModalCheckboxes;
  959  |     await expect(productCheckboxesB.first()).toBeVisible({ timeout: 10000 });
  960  | 
  961  |     let inactiveProductCheckboxB = null;
  962  |     const countB = await productCheckboxesB.count();
  963  |     for (let i = 0; i < countB; i++) {
  964  |       const checkbox = productCheckboxesB.nth(i);
  965  |       const value = await checkbox.getAttribute('value');
  966  |       if (value === inactiveProductValue) {
  967  |         inactiveProductCheckboxB = checkbox;
  968  |         break;
  969  |       }
  970  |     }
  971  | 
  972  |     expect(inactiveProductCheckboxB, 'Inactive product should not appear in Location B modal').toBeNull();
  973  |   });
  974  | 
  975  |   test(`TC:17299 - [Cancel order popup] Verify confirmation popup before canceling order line on planner`, async ({
  976  |     page,
  977  |   }) => {
  978  |     const user = await ordersPage.setupOrdererUserWithEnterpriseAccess(page, corpnav, ENTERPRISE_WITH_DATA_ID);
  979  |     userIdsToDelete.push(user.userId);
  980  |     await cleanupOrdersIfNeeded();
  981  | 
  982  |     // Prime onboarding flags + pin the enterprise selector so the freshly-created user is not routed off the
  983  |     // planner (the app bounces a fresh user to Home after their first order unless firstOrderDone is set),
  984  |     // then load it resiliently.
  985  |     await plannerPage.primeFreshUserForPlanner(page);
  986  |     await setEnterpriseSelectorInLocalStorage(page, ENTERPRISE_WITH_DATA_ID);
  987  |     await plannerPage.gotoPlannerStable(page);
  988  |     await showOnlyLastLocation(page);
  989  |     await plannerPage.switchOrderMode(page, true);
  990  |     // Settle live-sync BEFORE adding a product. An added product row is unsaved client state until an order
  991  |     // is placed; settling after it would let a live-sync "changes were discarded" push wipe the row.
  992  |     await plannerPage.settleLiveSync(page);
  993  |     await plannerPage.ensureMatrixHasOrderableProducts(page);
  994  |     await expect(planner.weekHeaders.first()).toBeVisible({ timeout: 30000 });
  995  | 
  996  |     // Self-seed a still-editable saved order through the UI. An API read-view order lands on the nearest
  997  |     // delivery date, which is usually past its order deadline and renders as a readonly cell that cannot
  998  |     // be zeroed out to exercise the cancel-deletion popup.
  999  |     const seeded = await plannerPage.seedSyncedOrderViaUI(page, '10');
  1000 |     const originalValue = seeded.value;
  1001 |     const inputWithSavedOrder = page.locator(`[data-testid="${seeded.testId}"]`);
  1002 | 
  1003 |     // Set the saved order line to 0. Per the ADO case the deletion confirmation appears only after
  1004 |     // pressing the Order button ("change it to 0 and press Order button" -> "Removing a quantity ...
  1005 |     // Are you sure?"), not on blur.
  1006 |     await inputWithSavedOrder.scrollIntoViewIfNeeded();
  1007 |     await inputWithSavedOrder.click();
  1008 |     await page.waitForTimeout(500);
  1009 |     await inputWithSavedOrder.fill('0');
  1010 |     await page.keyboard.press('Tab');
  1011 |     await page.waitForTimeout(1000);
  1012 | 
> 1013 |     await expect(planner.orderButton).toBeEnabled({ timeout: 30000 });
       |                                       ^ Error: expect(locator).toBeEnabled() failed
  1014 |     await plannerPage.clickOrderButton(page);
  1015 | 
  1016 |     await expect(planner.deletingQuantitiesModal).toBeVisible({ timeout: 10000 });
  1017 |     await expect(planner.deletingQuantitiesModalContent).toContainText(/Removing a quantity/i);
  1018 |     await expect(planner.deletingQuantitiesModalContent).toContainText(/Are you sure/i);
  1019 | 
  1020 |     await planner.deletingQuantitiesModalCancelButton.click();
  1021 |     await expect(planner.deletingQuantitiesModal).toBeHidden({ timeout: 10000 });
  1022 | 
  1023 |     await page.reload();
  1024 |     await expect(planner.weekHeaders.first()).toBeVisible({ timeout: 30000 });
  1025 | 
  1026 |     // Read back the cell this test seeded, not the first cell holding a value: the shared enterprise can
  1027 |     // carry other orders (e.g. when cleanup could not remove them), and their cells may render first.
  1028 |     const firstInputTestId = seeded.testId;
  1029 |     const firstInputAfterCancel = page.locator(`[data-testid="${firstInputTestId}"]`);
  1030 |     await firstInputAfterCancel.scrollIntoViewIfNeeded();
  1031 |     await expect(firstInputAfterCancel, 'Expected the seeded cell after cancel').toBeVisible({ timeout: 30000 });
  1032 |     const valueAfterCancel = await firstInputAfterCancel.inputValue();
  1033 |     expect(valueAfterCancel, 'Value should not be deleted after clicking Cancel').toBe(originalValue);
  1034 | 
  1035 |     await firstInputAfterCancel.click();
  1036 |     await firstInputAfterCancel.fill('0');
  1037 |     await page.keyboard.press('Tab');
  1038 |     await page.waitForTimeout(1000);
  1039 | 
  1040 |     await expect(planner.orderButton).toBeEnabled({ timeout: 30000 });
  1041 |     await plannerPage.clickOrderButton(page);
  1042 | 
  1043 |     await expect(planner.deletingQuantitiesModal).toBeVisible({ timeout: 10000 });
  1044 |     await planner.deletingQuantitiesModalConfirmButton.click();
  1045 |     await expect(planner.deletingQuantitiesModal).toBeHidden({ timeout: 10000 });
  1046 | 
  1047 |     await page.waitForTimeout(2000);
  1048 | 
  1049 |     const congratsModal = page.locator('.ant-modal:visible').filter({ hasText: /Congratulations/i });
  1050 |     const isCongratsVisible = await congratsModal.isVisible().catch(() => false);
  1051 |     if (isCongratsVisible) {
  1052 |       const closeButton = congratsModal.getByRole('button', { name: /Close/i });
  1053 |       await closeButton.click();
  1054 |       await expect(congratsModal).toBeHidden({ timeout: 10000 });
  1055 |     }
  1056 | 
  1057 |     // Verify the TRUE persisted state by reloading (ADO step 5: "Order quantity removed from Planner").
  1058 |     // NOTE (app behaviour, see bugs_found.md): a cell whose value was TYPED as "0" keeps displaying "0"
  1059 |     // in place until reload, and an order mutation can bounce a fresh user to Home - reloading reads the
  1060 |     // persisted result and re-establishes the enterprise context. Deleting the only seeded order also
  1061 |     // empties the matrix entirely (an added product row is unsaved once its order is gone), so the
  1062 |     // seeded cell is now either absent (matrix collapsed) or blank - both confirm the quantity is removed.
  1063 |     await plannerPage.dismissPostOrderModals(page);
  1064 |     await plannerPage.gotoPlannerStable(page);
  1065 |     await plannerPage.switchOrderMode(page, true);
  1066 |     await page.waitForLoadState('networkidle', { timeout: 10000 }).catch(() => {});
  1067 | 
  1068 |     const refreshedSavedInput = page.locator(`[data-testid="${firstInputTestId}"]`);
  1069 |     if ((await refreshedSavedInput.count()) > 0) {
  1070 |       const valueAfterConfirm = await refreshedSavedInput.inputValue();
  1071 |       expect(valueAfterConfirm, 'Value should be empty after confirming deletion').toBe('');
  1072 |     } else {
  1073 |       expect(
  1074 |         await planner.orderableInputs.count(),
  1075 |         'Deleting the only order should leave no orderable quantity on the planner',
  1076 |       ).toBe(0);
  1077 |     }
  1078 |   });
  1079 | 
  1080 |   test(`TC:14206 - [Cancellation] Check if zero can not be entered for order creation on Planner`, async ({ page }) => {
  1081 |     const user = await ordersPage.setupOrdererUserWithEnterpriseAccess(page, corpnav, ENTERPRISE_WITH_DATA_ID);
  1082 |     userIdsToDelete.push(user.userId);
  1083 | 
  1084 |     await page.goto(urls.orderPlanner);
  1085 |     await plannerPage.switchOrderMode(page, true);
  1086 |     await plannerPage.ensureMatrixHasOrderableProducts(page);
  1087 |     await expect(planner.weekHeaders.first()).toBeVisible({ timeout: 30000 });
  1088 | 
  1089 |     const allInputs = planner.orderableInputs;
  1090 |     await expect(allInputs.first()).toBeVisible({ timeout: 30000 });
  1091 | 
  1092 |     let emptyInputTestId = '';
  1093 |     const inputCount = await allInputs.count();
  1094 |     for (let i = 0; i < inputCount; i++) {
  1095 |       const input = allInputs.nth(i);
  1096 |       const isReadonly = await input.getAttribute('readonly');
  1097 |       if (isReadonly !== null) continue;
  1098 | 
  1099 |       const isEditable = await input.isEditable().catch(() => false);
  1100 |       if (!isEditable) continue;
  1101 | 
  1102 |       const value = await input.inputValue();
  1103 |       if (!value || value === '' || value === '0') {
  1104 |         emptyInputTestId = (await input.getAttribute('data-testid')) || '';
  1105 |         break;
  1106 |       }
  1107 |     }
  1108 | 
  1109 |     expect(emptyInputTestId, 'Expected to find an empty input field').toBeTruthy();
  1110 | 
  1111 |     const emptyInput = page.locator(`[data-testid="${emptyInputTestId}"]`);
  1112 |     await emptyInput.scrollIntoViewIfNeeded();
  1113 |     await expect(emptyInput).toBeEditable({ timeout: 10000 });
```