---
title: Konfigurace přístupu azure active directory – služba Azure Blockchain Service
description: Jak nakonfigurovat službu Azure Blockchain pomocí přístupu azure active directory
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 682ab282036fcd592e66942d08a84cdce46d8915
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455873"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Jak nakonfigurovat přístup k Azure Active Directory pro Službu Azure Blockchain

V tomto článku se dozvíte, jak udělit přístup a připojení k uzlům služby Azure Blockchain service pomocí uživatelských, skupinových nebo aplikačních ID služby Azure Active Directory (Azure AD).

Azure AD poskytuje cloudovou správu identit a umožňuje používat jednu identitu v celém podniku a přístup k aplikacím v Azure. Služba Azure Blockchain je integrovaná s Azure AD a nabízí výhody, jako je federace ID, jednotné přihlašování a vícefaktorové ověřování.

## <a name="prerequisites"></a>Požadavky

* [Vytvoření člena blockchainu pomocí portálu Azure](create-member.md)

## <a name="grant-access"></a>Udělení přístupu

Přístup můžete udělit na úrovni člena i na úrovni uzlu. Udělení přístupových práv na úrovni člena zase udělí přístup ke všem uzlům v rámci člena.

### <a name="grant-member-level-access"></a>Udělit přístup na úrovni členů

Udělení oprávnění k přístupu na úrovni člena.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Přejděte na **ovládací prvek Přístup (IAM) > Přidat > Přidat přiřazení role**.
1. Vyberte roli **Přístup k uzlu člena blockchainu (Preview)** a přidejte objekt Azure AD ID, ke kterým chcete udělit přístup. Objekt ID služby Azure a může být:

    | Objekt Azure AD | Příklad |
    |-----------------|---------|
    | Uživatel Azure AD   | `kim@contoso.onmicrosoft.com` |
    | Skupina Azure AD  | `sales@contoso.onmicrosoft.com` |
    | ID aplikace  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Přidat přiřazení role](./media/configure-aad/add-role-assignment.png)

1. Vyberte **Uložit**.

### <a name="grant-node-level-access"></a>Udělit přístup na úrovni uzlu

Přístup na úrovni uzlu můžete udělit tak, že přejdete na zabezpečení uzlu a kliknete na název uzlu, kterému chcete udělit přístup.

Vyberte roli Přístup k uzlu člena blockchainu (Preview) a přidejte objekt Azure AD ID, ke kterým chcete udělit přístup.

Další informace najdete [v tématu Konfigurace transakčních uzlů služby Azure Blockchain](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Připojení pomocí konektoru Azure Blockchain

Stáhněte si nebo naklonujte [konektor Azure Blockchain connector z GitHubu](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Postupujte podle úvodního startu části v **readme** k vytvoření konektoru ze zdrojového kódu.

### <a name="connect-using-an-azure-ad-user-account"></a>Připojení pomocí uživatelského účtu Azure AD

1. Spusťte následující příkaz k ověření pomocí uživatelského účtu Azure AD. Nahraďte \<myAADDirectory\> doménou Azure AD. Například, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD vyzve k zadání přihlašovacích údajů.
1. Přihlaste se pomocí svého uživatelského jména a hesla.
1. Po úspěšném ověření se místní proxy server připojí k vašemu uzlu blockchain. Nyní můžete připojit klienta Geth s místním koncovým bodem.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Připojení pomocí ID aplikace

Mnoho aplikací se ověřuje pomocí Azure AD pomocí ID aplikace namísto uživatelského účtu Azure AD.

Chcete-li se k uzlu připojit pomocí ID aplikace, nahraďte **aadauthcode** **klientem aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parametr | Popis |
|-----------|-------------|
| id klienta | Doména Azure AD, například`yourdomain.onmicrosoft.com`
| id klienta | ID klienta registrované aplikace ve službě Azure AD
| tajný klíč klienta | Tajný klíč klienta registrované aplikace ve službě Azure AD

Další informace o tom, jak zaregistrovat aplikaci ve službě Azure AD, najdete v [tématu Postup: Pomocí portálu k vytvoření aplikace Azure AD a instančního objektu, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Připojení mobilního zařízení nebo textového prohlížeče

Pro mobilní zařízení nebo textový prohlížeč, kde není možné zobrazení automaticky otevíraného okna ověřování Azure AD, Azure AD vygeneruje jednorázový přístupový kód. Můžete zkopírovat přístupový kód a pokračovat s ověřováním Azure AD v jiném prostředí.

Chcete-li vygenerovat přístupový kód, nahraďte **aadauthcode** **zařízením aaddevice**. Nahraďte \<myAADDirectory\> doménou Azure AD. Například, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení dat ve službě Azure Blockchain Service najdete v tématu [Zabezpečení služby Azure Blockchain .](data-security.md)