---
title: Jak nakonfigurovat přístup k Azure Active Directory
description: Jak nakonfigurovat službu Azure Blockchain s přístupem Azure Active Directory
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: seal
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: 616e342f1d52179c40c225c5dafc9de13ce85e06
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028212"
---
# <a name="how-to-configure-azure-active-directory-access"></a>Jak nakonfigurovat přístup k Azure Active Directory

V tomto článku se dozvíte, jak udělit přístup a připojení k uzlům služba Blockchain v Azure pomocí uživatele, skupiny nebo ID aplikace Azure Active Directory (Azure AD).

Azure AD poskytuje správu identit v cloudu a umožňuje používat jednu identitu napříč celé aplikace enterprise a přístup v Azure. Služba Azure Blockchain integrovat se službou Azure AD a nabízí výhody, jako je například ID federace, ověření jednotného přihlašování a službou Multi-Factor Authentication.

## <a name="prerequisites"></a>Požadavky

* [Vytvoření blockchainové členu pomocí webu Azure portal](create-member.md)

## <a name="grant-access"></a>Udělení přístupu

Můžete udělit přístup na úrovni člena i na úrovni uzlu. Udělení oprávnění na úrovni člena zase udělí přístup do všech uzlů v rámci člena.

### <a name="grant-member-level-access"></a>Udělení přístupu na úrovni člena

Chcete-li udělit přístupová oprávnění na úrovni člena.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Přejděte do **řízení přístupu (IAM) > Přidat > Přidat přiřazení role**.
1. Vyberte **Blockchain člen uzlu přístup (Preview)** role a přidejte objekt Azure AD ID, kterým chcete přiřadit přístup k. Azure AD ID objektu může být:

    | Objekt Azure AD | Příklad: |
    |-----------------|---------|
    | Uživatele Azure AD   | `frank@contoso.onmicrosoft.com` |
    | Skupiny Azure AD  | `sales@contoso.onmicrosoft.com` |
    | ID aplikace  | `13925ab1-4161-4534-8d18-812f5ca1ab1e` |

    ![Přidat přiřazení role](./media/configure-aad/add-role-assignment.png)

1. Vyberte **Uložit**.

### <a name="grant-node-level-access"></a>Udělení přístupu na úrovni uzlu

1. Můžete udělit přístup na úrovni uzlu tak, že přejdete do uzlu zabezpečení a klikněte na název uzlu, který chcete udělit přístup.
1. Vyberte roli Blockchain člen uzlu přístup (Preview) a přidejte objekt Azure AD ID, kterou chcete udělit přístup k. 

## <a name="connect-using-azure-blockchain-connector"></a>Připojit pomocí konektoru služby Azure Blockchain

Stáhněte nebo naklonujte [Azure Blockchain konektor z Githubu](https://github.com/Microsoft/azure-blockchain-connector/).

```bash
git clone https://github.com/Microsoft/azure-blockchain-connector.git
```

Postupujte podle tohoto rychlého startu tématu **readme** k vytvoření konektoru ze zdrojového kódu.

### <a name="connect-using-an-azure-ad-user-account"></a>Připojte se pomocí uživatelského účtu Azure AD

1. Spuštěním následujícího příkazu proveďte ověření pomocí účtu uživatele Azure AD. Nahraďte \<myAADDirectory\> s doménou Azure AD. Například, `yourdomain.onmicrosoft.com`.

    ```
    connector.exe -remote <myMemberName>.blockchain.azure.com:3200 -method aadauthcode -tenant-id <myAADDirectory> 
    ```

1. Azure AD vyzve k zadání přihlašovacích údajů.
1. Přihlaste se pomocí uživatelského jména a hesla.
1. Po úspěšném ověření váš místní proxy server připojí k uzlu blockchain. Nyní můžete připojit váš klient Geth s místním koncovým bodem.

    ```bash
    geth attach http://127.0.0.1:3100
    ```

### <a name="connect-using-an-application-id"></a>Připojte se pomocí ID aplikace.

Mnoho aplikací se ověřování pomocí Azure AD pomocí ID aplikace místo účtu uživatele Azure AD.

Chcete-li připojit k uzlu pomocí ID aplikace, nahraďte **aadauthcode** s **aadclient**.

```
connector.exe -remote <myBlockchainEndpoint>  -method aadclient -client-id <myClientID> -client-secret "<myClientSecret>" -tenant-id <myAADDirectory>
```

| Parametr | Popis |
|-----------|-------------|
| id tenanta | Doména služby Azure AD, například `yourdomain.onmicrosoft.com`
| id klienta | ID klienta registrované aplikace ve službě Azure AD
| client-secret | Tajný kód klienta registrované aplikace ve službě Azure AD

Další informace o tom, jak zaregistrovat aplikaci v Azure AD najdete v tématu [jak: Použití portálu k vytvoření aplikace Azure AD a instanční objekt, který má přístup k prostředkům](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="connect-a-mobile-device-or-text-browser"></a>Připojení mobilních zařízení nebo text prohlížeče

Pro mobilní zařízení nebo založený na textu prohlížeče, kde není možné zobrazovat místní ověřování Azure AD Azure AD vygeneruje jednorázové heslo. Můžete zkopírovat přístupový kód a pokračujte v ověřování Azure AD v jiném prostředí.

Chcete-li vygenerovat přístupový kód, nahraďte **aadauthcode** s **aaddevice**. Nahraďte \<myAADDirectory\> s doménou Azure AD. Například, `yourdomain.onmicrosoft.com`.

```
connector.exe -remote <myBlockchainEndpoint>  -method aaddevice -tenant-id <myAADDirectory>
```

## <a name="next-steps"></a>Další postup

Další informace o zabezpečení dat ve službě Azure Blockchain naleznete v tématu:

> [!div class="nextstepaction"]
> [Azure Blockchain služby zabezpečení](data-security.md)