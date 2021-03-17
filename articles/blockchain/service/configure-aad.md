---
title: Konfigurace přístupu Azure Active Directory – služba Azure blockchain
description: Jak nakonfigurovat službu Azure blockchain pomocí přístupu Azure Active Directory
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 337d01abc51d310d06aeea3427b770132be4824c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85208769"
---
# <a name="how-to-configure-azure-active-directory-access-for-azure-blockchain-service"></a>Jak nakonfigurovat přístup Azure Active Directory ke službě Azure blockchain

V tomto článku se dozvíte, jak udělit přístup a připojit se k uzlům služby Azure blockchain pomocí Azure Active Directory (Azure AD) ID uživatelů, skupin nebo aplikací.

Azure AD poskytuje cloudovou správu identit a umožňuje používat jedinou identitu v celém podniku a přistupovat k aplikacím v Azure. Služba Azure Blockchain je integrovaná se službou Azure AD a nabízí výhody, jako je federace IDENTIFIKÁTORů, jednotné přihlašování a ověřování Multi-Factor Authentication.

## <a name="prerequisites"></a>Požadavky

* [Vytvoření členu blockchain pomocí Azure Portal](create-member.md)

## <a name="grant-access"></a>Udělení přístupu

Přístup můžete udělit na úrovni člena i na úrovni uzlu. Udělení přístupových práv na úrovni členů umožní udělit přístup ke všem uzlům v rámci člena.

### <a name="grant-member-level-access"></a>Udělení přístupu na úrovni člena

Udělení oprávnění k přístupu na úrovni člena.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Přejděte na **řízení přístupu (IAM) > přidat > přidat přiřazení role**.
1. Vyberte roli **přístup k členskému uzlu blockchain (Preview)** a přidejte objekt Azure AD ID, ke kterému chcete udělit přístup. Objekt Azure AD ID může být:

    | Objekt Azure AD | Příklad |
    |-----------------|---------|
    | Uživatel služby Azure AD   | `kim@contoso.onmicrosoft.com` |
    | Skupina Azure AD  | `sales@contoso.onmicrosoft.com` |
    | ID aplikace  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Přidat přiřazení role](./media/configure-aad/add-role-assignment.png)

1. Vyberte **Uložit**.

### <a name="grant-node-level-access"></a>Udělení přístupu na úrovni uzlu

Přístup můžete udělit na úrovni uzlu tak, že přejdete na zabezpečení uzlu a kliknete na název uzlu, kterému chcete udělit přístup.

Vyberte roli přístup k členskému uzlu blockchain (Preview) a přidejte objekt Azure AD ID, ke kterému chcete udělit přístup.

Další informace najdete v tématu [Konfigurace uzlů transakcí služby Azure blockchain](configure-transaction-nodes.md#azure-active-directory-access-control).

## <a name="connect-using-azure-blockchain-connector"></a>Připojit pomocí konektoru Azure blockchain

Stáhněte nebo naklonujte [konektor Azure blockchain z GitHubu](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Postupujte podle pokynů v části rychlý Start v **souboru Readme** a sestavte konektor ze zdrojového kódu.

### <a name="connect-using-an-azure-ad-user-account"></a>Připojení pomocí uživatelského účtu Azure AD

1. Spuštěním následujícího příkazu proveďte ověření pomocí uživatelského účtu Azure AD. Nahraďte \<myAADDirectory\> doménou služby Azure AD. Například, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Služba Azure AD vyzve k zadání přihlašovacích údajů.
1. Přihlaste se pomocí svého uživatelského jména a hesla.
1. Po úspěšném ověření se místní proxy server připojí k vašemu blockchain uzlu. Nyní můžete připojit klienta Geth k místnímu koncovému bodu.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Připojit pomocí ID aplikace

Mnoho aplikací se ověřuje pomocí Azure AD pomocí ID aplikace namísto uživatelského účtu Azure AD.

Pokud se chcete připojit k uzlu pomocí ID aplikace, nahraďte **aadauthcode** **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parametr | Popis |
|-----------|-------------|
| ID tenanta | Doména služby Azure AD, například `yourdomain.onmicrosoft.com`
| ID klienta | ID klienta registrované aplikace ve službě Azure AD
| tajný kód klienta | Tajný kód klienta registrované aplikace ve službě Azure AD

Další informace o tom, jak zaregistrovat aplikaci v Azure AD, najdete v tématu [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům.](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Připojit mobilní zařízení nebo textový prohlížeč

Pro mobilní zařízení nebo textový prohlížeč, kde se nepodporuje místní zobrazení pro ověřování Azure AD, Azure AD vygeneruje jednorázové heslo. Můžete zkopírovat heslo a pokračovat v ověřování Azure AD v jiném prostředí.

Pokud chcete vygenerovat heslo, nahraďte **aadauthcode** řetězcem **aaddevice**. Nahraďte \<myAADDirectory\> doménou služby Azure AD. Například, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Další kroky

Další informace o zabezpečení dat ve službě Azure blockchain najdete v článku [zabezpečení služby Azure blockchain](data-security.md).