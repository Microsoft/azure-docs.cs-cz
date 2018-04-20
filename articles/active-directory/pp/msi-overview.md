---
title: Identita spravované služby (MSI) pro Azure Active Directory
description: Přehled identita spravované služby pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ms.reviewer: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 95980c082b09ad959ab8bbaae0250b40ac08d2c8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Spravovaná služba Identity (MSI) pro prostředky Azure

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Běžné výzvu, při vytváření cloudové aplikace je Správa přihlašovacích údajů, musí být v kódu ověřování pro cloudové služby. Zachování zabezpečení těchto přihlašovacích údajů je důležitá úloha. V ideálním případě by se nikdy se zobrazí na pracovních stanicích vývojáře nebo získat změnami do správy zdrojového kódu. Azure Key Vault poskytuje způsob, jak bezpečně uložit přihlašovací údaje a další klíče a tajné klíče, je však nutné kódu k ověření Key Vault je znovu načíst. Identita spravované služby (MSI) usnadňuje řešení tohoto problému jednodušší tím, že služby Azure automaticky spravované identity v Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault, bez nutnosti všechny přihlašovací údaje ve vašem kódu.

## <a name="how-does-it-work"></a>Jak to funguje?

Pokud používáte identita spravované služby v instanci služby Azure, Azure vytvoří identity v klientovi Azure AD používané vašeho předplatného Azure. Kromě toho Azure zřídí přihlašovací údaje pro identitu, do instance služby. V důsledku toho kód pak můžete provést místní požadavek na načtení přístupových tokenů pro služby, které podporují ověřování Azure AD. Při Azure má na starosti vrácení pověření používaná v instanci služby.

## <a name="how-do-i-enable-my-resources-to-use-a-managed-service-identity"></a>Povolení Moje prostředky na používání Identity spravované služby

Nejsou k dispozici dva typy spravované služby identit: *přiřazené systému* a *uživatel přiřazenou*.

- A **přiřazené systému** MSI je povoleno přímo v instanci služby Azure. Proces povolení Azure vytvoří identity pro instanci služby v klientovi Azure AD a zřídí přihlašovací údaje pro identitu do instance služby. Životní cyklus systému přiřazené MSI je přímo vázaný na Azure instance služby je nastaveno na. Pokud instance služby se odstraní, Azure automaticky vyčistí přihlašovací údaje a identitu ve službě Azure AD.

- A **uživatel přiřazenou** MSI *(soukromém náhledu)* je vytvořen jako samostatný prostředků Azure. Proces vytvoření Azure vytvoří identity v klientovi Azure AD. Po vytvoření identity jej můžete přiřadit jednu nebo více instancí služby Azure. Vzhledem k tomu, že instalační služby MSI přiřazený uživatelem mohou využívat více instancí služby Azure, životní cyklus spravované samostatně.

Tady je příklad toho, jak funguje MSI se přiřazené systému s virtuálními počítači Azure.

![Příklad MSI virtuálního počítače](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager obdrží zprávu, která povolit MSI přiřadil systém na virtuálním počítači.
2. Azure Resource Manager vytvoří objekt služby ve službě Azure AD reprezentuje identitu virtuálního počítače. Objekt služby se vytvoří v klientovi Azure AD, který je důvěryhodný pro toto předplatné.
3. Azure Resource Manager nakonfiguruje Podrobnosti objektu služby v rozšíření virtuálního počítače MSI virtuálního počítače. Tento krok zahrnuje konfiguraci ID klienta a certifikát používaný službou rozšíření k získání přístupových tokenů z Azure AD.
4. Teď, když se označuje identity instanční objekt virtuálního počítače, můžete udělit přístup k prostředkům Azure. Například pokud váš kód potřebuje volat Azure Resource Manager, pak by přiřadíte instanční objekt Virtuálního počítače roli odpovídající pomocí řízení přístupu na základě Role (RBAC) ve službě Azure AD.  Pokud váš kód potřebuje volat Key Vault, by udělit přístup kódu k určitého tajného klíče nebo klíče v Key Vault.
5. Váš kód spuštěný ve virtuálním počítači požadavky token z místní koncový bod, který je hostitelem rozšíření virtuálního počítače MSI: http://localhost:50342/oauth2/token. Parametr prostředku určuje službu, na kterou je odesláno token. Například pokud chcete, aby váš kód pro ověření do Azure Resource Manageru, byste použili prostředků =https://management.azure.com/.
6. Rozšíření virtuálního počítače MSI používá jeho ID konfigurovaného klienta a certifikát k vyžádání tokenu přístupu z Azure AD.  Azure AD vrátí přístupový token JSON Web Token (JWT).
7. Váš kód odešle přístupového tokenu pro volání na službu, která podporuje ověřování Azure AD.

Pomocí stejné diagramu, tady je příklad toho, jak funguje instalační služby MSI přiřazený uživatelem s virtuálními počítači Azure.

![Příklad MSI virtuálního počítače](~/articles/active-directory/media/msi-vm-example.png)

1. Azure Resource Manager obdrží zprávu vytvořit MSI se přiřazený uživatelem.
2. Azure Resource Manager vytvoří objekt služby ve službě Azure AD představují identitu soubor MSI. Objekt služby se vytvoří v klientovi Azure AD, který je důvěryhodný pro toto předplatné.
3. Azure Resource Manager obdrží zprávu, která se v rozšíření virtuálního počítače MSI virtuálního počítače, nakonfigurujte podrobnosti instanční objekt. Tento krok zahrnuje konfiguraci ID klienta a certifikát používaný službou rozšíření k získání přístupových tokenů z Azure AD.
4. Teď, když se označuje instanční objekt identity soubor MSI, můžete udělit přístup k prostředkům Azure. Například pokud váš kód potřebuje volat Azure Resource Manager, pak by přiřadíte MSI objektu služby roli odpovídající pomocí řízení přístupu na základě Role (RBAC) ve službě Azure AD. Pokud váš kód potřebuje volat Key Vault, by udělit přístup kódu k určitého tajného klíče nebo klíče v Key Vault. Poznámka: Krok 3 se nevyžaduje k dokončení kroku 4. Jakmile MSI existuje, můžete udělit přístup k prostředkům, bez ohledu na to, konfigurován na virtuálním počítači, nebo ne.
5. Váš kód spuštěný ve virtuálním počítači požadavky token z místní koncový bod, který je hostitelem rozšíření virtuálního počítače MSI: http://localhost:50342/oauth2/token. Parametr ID klienta Určuje název MSI identity použít. Kromě toho určuje parametr prostředku služby, ke které je odeslána token. Například pokud chcete, aby váš kód pro ověření do Azure Resource Manageru, byste použili prostředků =https://management.azure.com/.
6. Rozšíření virtuálního počítače MSI zkontroluje, zda je nakonfigurovaný certifikát pro ID požadované klienta a požadavky přístupový token ze služby Azure AD. Azure AD vrátí přístupový token JSON Web Token (JWT).
7. Váš kód odešle přístupového tokenu pro volání na službu, která podporuje ověřování Azure AD.

Každý služba Azure, která podporuje identita spravované služby má svou vlastní metoda kódu získat přístupový token. Podívejte se na kurzy pro každou službu a zjistěte, konkrétní metody k získání tokenu.

## <a name="try-managed-service-identity"></a>Zkuste identita spravované služby

Zkuste identita spravované služby kurzu se dozvíte začátku do konce scénáře pro přístup k různými prostředky Azure:
<br><br>
| Z prostředků povoleno MSI | Získáte informace o těchto tématech: |
| ------- | -------- |
| Virtuální počítač Azure (Linux)   | [Správce přístupu k prostředku Azure s virtuálního počítače s Linuxem identita spravované služby](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Přístup k úložišti Azure přes přístupový klíč s identitou služby spravované Linux virtuálního počítače](msi-tutorial-linux-vm-access-storage.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Které služby Azure podporuje identita spravované služby?

Služby Azure, které podporují spravované identit služby slouží k ověření služby, které podporují ověřování Azure AD MSI.  Probíhá integrace MSI a Azure AD ověřování přes Azure.  Zkontrolujte zpět často aktualizací.

### <a name="azure-services-that-support-managed-service-identity"></a>Služby Azure, které podporují identita spravované služby

Následující služby Azure podporují identita spravované služby.

| Služba | Status | Datum | Konfigurace | Získání tokenu |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Preview | 2017 září | [Azure CLI](msi-qs-configure-cli-windows-vm.md)<br>[Šablony Azure Resource Manageru](msi-qs-configure-template-windows-vm.md) | [Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[HTTP/REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure services, ověření podpory Azure AD

Následující služby podporují ověřování Azure AD a byly testovány s klienta služby, které používají identita spravované služby.

| Služba | ID prostředku | Status | Datum | Přiřadit přístup pro |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com/ | Dostupné | 2017 září | [Azure CLI](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net/ | Dostupné | 2017 září | |
| Azure Data Lake | https://datalake.azure.net/ | Dostupné | 2017 září | |
| Azure SQL | https://database.windows.net/ | Dostupné | Říjen 2017 | |

## <a name="how-much-does-managed-service-identity-cost"></a>Kolik identita spravované služby stojí?

Identita spravované služby se dodává s Azure Active Directory volná, což je výchozí nastavení pro předplatná Azure.  Není k dispozici bez dalších nákladů spravované identitu služby.

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Rádi od vás uslyšíme!

* Postupy: dotazy posílejte na Stack Overflow ke značce [azure msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Zajistěte, aby funkce žádostí nebo na váš názor [fóru pro zpětnou vazbu Azure AD pro vývojáře](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






