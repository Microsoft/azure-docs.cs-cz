---
title: Udělení oprávnění aplikacím pro přístup k trezoru klíčů Azure pomocí Azure RBAC | Microsoft Docs
description: Naučte se poskytovat přístup k klíčům, tajným klíčům a certifikátům pomocí řízení přístupu na základě role v Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 8/30/2020
ms.author: mbaldwin
ms.openlocfilehash: 886b87adeabdc0aadde04c189b78739435aabede
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100527017"
---
# <a name="provide-access-to-key-vault-keys-certificates-and-secrets-with-an-azure-role-based-access-control"></a>Poskytnutí přístupu k klíčům Key Vault, certifikátům a tajným klíčům pomocí řízení přístupu na základě role Azure

> [!NOTE]
> Poskytovatel prostředků Key Vault podporuje dva typy prostředků: **trezory** a **spravované HSM**. Řízení přístupu popsané v tomto článku platí jenom pro **trezory**. Další informace o řízení přístupu pro spravovaný modul HSM najdete v tématu [spravované řízení přístupu HSM](../managed-hsm/access-control.md).

Řízení přístupu na základě role Azure (Azure RBAC) je autorizační systém založený na [Azure Resource Manager](../../azure-resource-manager/management/overview.md) , který poskytuje jemně odstupňovanou správu prostředků Azure.

Azure RBAC umožňuje uživatelům spravovat klíče, tajné klíče a oprávnění k certifikátům. Poskytuje jedno místo pro správu všech oprávnění napříč všemi trezory klíčů. 

Model Azure RBAC nabízí možnost nastavit oprávnění na různých úrovních oboru: skupina pro správu, předplatné, skupina prostředků nebo jednotlivé prostředky.  Azure RBAC pro Trezor klíčů taky nabízí možnost mít samostatná oprávnění pro jednotlivé klíče, tajné klíče a certifikáty.

Další informace najdete v tématu [řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="best-practices-for-individual-keys-secrets-and-certificates"></a>Osvědčené postupy pro jednotlivé klíče, tajné klíče a certifikáty

Naším doporučením je použití trezoru pro jednotlivé aplikace (vývoj, předprodukční a produkční prostředí).

Oprávnění k jednotlivým klíčům, tajným klíčům a certifikátům by se měly používat jenom pro konkrétní scénáře:

-   Vícevrstvé aplikace, které vyžadují oddělení řízení přístupu mezi vrstvami

-   Sdílení jednotlivých tajných kódů mezi několika aplikacemi

Další informace o pokynech pro správu Azure Key Vault najdete v tématech:

- [Přehled zabezpečení Azure Key Vault](security-overview.md)
- [Omezení služby Azure Key Vault](service-limits.md)

## <a name="azure-built-in-roles-for-key-vault-data-plane-operations"></a>Předdefinované role Azure pro operace Key Vault roviny dat
> [!NOTE]
> `Key Vault Contributor` role je určena pro operace roviny správy ke správě trezorů klíčů. Neumožňuje přístup k klíčům, tajným klíčům a certifikátům.

| Předdefinovaná role | Popis | ID |
| --- | --- | --- |
| Správce Key Vault| Proveďte všechny operace roviny dat u trezoru klíčů a všech objektů, včetně certifikátů, klíčů a tajných klíčů. Nejde spravovat prostředky trezoru klíčů ani spravovat přiřazení rolí. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | 00482a5a-887f-4fb3-b363-3b7fe8e74483 |
| Key Vault úřední certifikát | S výjimkou oprávnění spravovat je možné provést jakoukoli akci s certifikáty trezoru klíčů. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | a4417e6f-fecd-4de8-b567-7b0420556985 |
| Key Vault kryptografický pracovník | Proveďte jakoukoli akci s klíči trezoru klíčů s výjimkou oprávnění ke správě. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | 14b46e9e-c2b7-41b4-b07b-48a6ebf60603 |
| Uživatel šifrování Key Vault šifrovací služby | Čtení metadat klíčů a provádění operací zalamování nebo rozbalení. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | e147488a-f6f5-4113-8e2d-b22465e65bf6 |
| Key Vault kryptografický uživatel  | Provádějte kryptografické operace pomocí klíčů. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | 12338af0-0e69-4776-bea7-57ae8d297424 |
| Čtecí modul Key Vault | Číst metadata trezorů klíčů a jeho certifikátů, klíčů a tajných klíčů. Nelze číst citlivé hodnoty, jako je například obsah tajného klíče nebo klíčové materiály. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | 21090545-7ca7-4776-b22c-e363652d74d2 |
| Úředník Key Vault tajných klíčů| Vykoná jakoukoli akci s tajnými kódy trezoru klíčů s výjimkou oprávnění ke správě. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | b86a8fe4-44ce-4948-aee5-eccb2c155cd7 |
| Uživatel Key Vault tajných klíčů | Číst tajný obsah. Funguje jenom pro trezory klíčů, které používají model oprávnění řízení přístupu na základě role Azure. | 4633458b-17de-408a-b874-0445c86b69e6 |

Další informace o definicích integrovaných rolí Azure najdete v tématu [předdefinované role Azure](../../role-based-access-control/built-in-roles.md).

## <a name="using-azure-rbac-secret-key-and-certificate-permissions-with-key-vault"></a>Použití oprávnění tajných klíčů, klíčů a certifikátů Azure RBAC s Key Vault

Nový model oprávnění Azure RBAC pro Trezor klíčů poskytuje alternativu k modelu oprávnění zásad přístupu trezoru. 

### <a name="prerequisites"></a>Požadavky

Chcete-li přidat přiřazení rolí, je nutné mít následující:

- Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- `Microsoft.Authorization/roleAssignments/write`a `Microsoft.Authorization/roleAssignments/delete` oprávnění, jako je například správce nebo [vlastník](../../role-based-access-control/built-in-roles.md#owner) [přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator)

### <a name="enable-azure-rbac-permissions-on-key-vault"></a>Povolit oprávnění Azure RBAC na Key Vault

> [!NOTE]
> Změna modelu oprávnění vyžaduje oprávnění "Microsoft. Authorization/roleAssignments/Write", která je součástí rolí [vlastník](../../role-based-access-control/built-in-roles.md#owner) a [Správce přístupu uživatele](../../role-based-access-control/built-in-roles.md#user-access-administrator) . Role správce pro klasický odběr, jako je správce služeb a spolusprávce, se nepodporují.

1.  Povolit oprávnění Azure RBAC pro nový trezor klíčů:

    ![Povolit oprávnění Azure RBAC – nový trezor](../media/rbac/image-1.png)

2.  Povolit oprávnění Azure RBAC pro existující Trezor klíčů:

    ![Povolit oprávnění Azure RBAC – existující trezor](../media/rbac/image-2.png)

> [!IMPORTANT]
> Nastavení modelu oprávnění Azure RBAC neověřuje všechna oprávnění zásad přístupu. Může to způsobit výpadky, když nejsou přiřazené ekvivalentní role Azure.

### <a name="assign-role"></a>Přiřazení role

> [!Note]
> Místo názvu role ve skriptech doporučujeme použít jedinečné ID role. Proto pokud je role přejmenována, budou vaše skripty nadále fungovat. Tento název role dokumentu se používá jenom pro čitelnost.

Příkaz Azure CLI pro vytvoření přiřazení role:

```azurecli
az role assignment create --role <role_name_or_id> --assignee <assignee> --scope <scope>
```

V Azure Portal je obrazovka přiřazení rolí Azure dostupná pro všechny prostředky na kartě řízení přístupu (IAM).

![Přiřazení role – karta IAM](../media/rbac/image-3.png)

### <a name="resource-group-scope-role-assignment"></a>Přiřazení role oboru skupiny prostředků

1.  Přejít do skupiny prostředků trezoru klíčů
    ![Přiřazení role – skupina prostředků](../media/rbac/image-4.png)

2.  Klikněte na řízení přístupu (IAM) přidat \> přiřazení role \> Přidat.

3.  Vytvořit roli čtecího zařízení Key Vault "Key Vault Reader" pro aktuálního uživatele

    ![Přidat roli – skupina prostředků](../media/rbac/image-5.png)

Rozhraní příkazového řádku Azure:
```azurecli
az role assignment create --role "Key Vault Reader" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}
```

Výše uvedené přiřazení role poskytuje možnost vypsat objekty trezoru klíčů v trezoru klíčů.

### <a name="key-vault-scope-role-assignment"></a>Přiřazení role oboru Key Vault

1. Přejít na \> kartu řízení přístupu k Key Vault (IAM)

2. Klikněte na přidat přiřazení role \> Přidat.

3. Vytvořit roli důstojníka klíčová tajná zpráva "Key Vault tajných klíčů" pro aktuálního uživatele.

    ![Přiřazení role – Trezor klíčů](../media/rbac/image-6.png)

 Rozhraní příkazového řádku Azure:

```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e jalichwa@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}
```

Po vytvoření výše uvedeného přiřazení role můžete vytvářet, aktualizovat a odstraňovat tajné klíče.

4. Vytvoří nový tajný klíč (tajné klíče \> + generování/import) pro testování přiřazení role tajného klíče.

    ![Přidat roli – Trezor klíčů](../media/rbac/image-7.png)

### <a name="secret-scope-role-assignment"></a>Přiřazení role oboru tajného klíče

1. Otevřete jedno z dříve vytvořených tajných klíčů, přehled a řízení přístupu (IAM). 

2. Kliknout na kartu řízení přístupu (IAM)

    ![Přiřazení role – tajný kód](../media/rbac/image-8.png)

3. Vytvořit roli důstojníka klíče tajných klíčů "Key Vault tajných klíčů" pro aktuálního uživatele, stejně jako u Key Vault.

Rozhraní příkazového řádku Azure:

```azurecli
az role assignment create --role "Key Vault Secrets Officer" --assignee {i.e user@microsoft.com} --scope /subscriptions/{subscriptionid}/resourcegroups/{resource-group-name}/providers/Microsoft.KeyVault/vaults/{key-vault-name}/secrets/RBACSecret
```

### <a name="test-and-verify"></a>Testování a ověření

> [!NOTE]
> Po odebrání přiřazení rolí se vyžaduje, aby se v prohlížečích používalo ukládání do mezipaměti a aktualizace stránky.<br>
> Počkejte několik minut, než se přiřazení role aktualizuje.

1. Ověří přidání nového tajného klíče bez role Key Vault tajných klíčů na úrovni trezoru klíčů.

Přejděte na kartu IAM (Správa přístupu k trezoru klíčů) a odeberte přiřazení role Key Vault tajných klíčů důstojníka pro tento prostředek.

![Odebrat přiřazení – Trezor klíčů](../media/rbac/image-9.png)

Přejděte k dříve vytvořenému tajnému kódu. Můžete zobrazit všechny vlastnosti tajného klíče.

![Zobrazení tajného klíče s přístupem](../media/rbac/image-10.png)

Vytvořit nový tajný klíč (tajné klíče \> + Generovat/importovat) by se měl zobrazit pod chybou:

   ![Vytvořit nový tajný kód](../media/rbac/image-11.png)

2.  Ověří úpravy tajného klíče bez role "Key Vault tajná osoba" na úrovni tajného klíče.

-   Přejít na dříve vytvořenou kartu IAM (Secret Access Control) a odebrat přiřazení role "Key Vault tajného klíče" pro tento prostředek.

-   Přejděte k dříve vytvořenému tajnému kódu. Můžete zobrazit vlastnosti tajného klíče.

   ![Zobrazení tajného klíče bez přístupu](../media/rbac/image-12.png)

3. Ověření tajných kódů bez role čtenář na úrovni trezoru klíčů

-   Přejděte na kartu IAM (Správa přístupu ke skupině prostředků trezoru klíčů) a odeberte přiřazení role Key Vault Reader.

-   Navigace na kartě tajné klíče trezoru klíčů by se měla zobrazit pod chybou:

   ![Karta tajné – chyba](../media/rbac/image-13.png)

### <a name="creating-custom-roles"></a>Vytváření vlastních rolí 

[AZ role definition Create – příkaz](/cli/azure/role/definition#az-role-definition-create)

**(Skript CLI bash)</br>**
```azurecli
az role definition create --role-definition '{ \
   "Name": "Backup Keys Operator", \
   "Description": "Perform key backup/restore operations", \
    "Actions": [ 
    ], \
    "DataActions": [ \
        "Microsoft.KeyVault/vaults/keys/read ", \
        "Microsoft.KeyVault/vaults/keys/backup/action", \
         "Microsoft.KeyVault/vaults/keys/restore/action" \
    ], \
    "NotDataActions": [ 
   ], \
    "AssignableScopes": ["/subscriptions/{subscriptionId}"] \
}'
```

Další informace o tom, jak vytvářet vlastní role, najdete v těchto tématech:

[Vlastní role Azure](../../role-based-access-control/custom-roles.md)

## <a name="known-limits-and-performance"></a>Známá omezení a výkon

-   2000 přiřazení rolí Azure na předplatné

-   Latence přiřazení rolí: v aktuálním očekávaném výkonu bude trvat až 10 minut (600 sekund) po změně přiřazení rolí, aby se role používala.

## <a name="learn-more"></a>Další informace

- [Přehled Azure RBAC](../../role-based-access-control/overview.md)
- [Kurz pro vlastní role](../../role-based-access-control/tutorial-custom-role-cli.md)