---
title: "Identita spravované služby (MSI) pro Azure Active Directory"
description: "Přehled identita spravované služby pro prostředky Azure."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: 
ms.workload: identity
ms.date: 12/19/2017
ms.author: skwan
ms.openlocfilehash: 72e1d5250de214c063a970f5761105b2329ccf7f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2018
---
#  <a name="managed-service-identity-msi-for-azure-resources"></a>Spravovaná služba Identity (MSI) pro prostředky Azure

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Běžné výzvu, při vytváření cloudové aplikace je Správa přihlašovacích údajů, musí být v kódu ověřování pro cloudové služby. Zachování zabezpečení těchto přihlašovacích údajů je důležitá úloha. V ideálním případě by se nikdy se zobrazí na pracovních stanicích vývojáře nebo získat změnami do správy zdrojového kódu. Azure Key Vault poskytuje způsob, jak bezpečně uložit přihlašovací údaje a další klíče a tajné klíče, je však nutné kódu k ověření Key Vault je znovu načíst. Identita spravované služby (MSI) usnadňuje řešení tohoto problému jednodušší tím, že služby Azure automaticky spravované identity v Azure Active Directory (Azure AD). Tuto identitu můžete použít k ověření jakoukoli službu, která podporuje ověřování Azure AD, včetně Key Vault, bez nutnosti všechny přihlašovací údaje ve vašem kódu.

## <a name="how-does-it-work"></a>Jak to funguje?

Pokud povolíte identita spravované služby v služby Azure, Azure automaticky vytvoří identity pro instanci služby v klientovi Azure AD používané vašeho předplatného Azure.  V pozadí zřídí Azure přihlašovací údaje pro identitu do instance služby.  Váš kód pak můžete provést místní požadavek na načtení přístupových tokenů pro služby, které podporují ověřování Azure AD.  Azure má na starosti vrácení pověření používaná v instanci služby.  Pokud instance služby se odstraní, Azure automaticky vyčistí přihlašovací údaje a identitu ve službě Azure AD.

Tady je příklad toho, jak spravovat identitu služby funguje s virtuálními počítači Azure.

![Příklad MSI virtuálního počítače](./media/msi-vm-example.png)

1. Azure Resource Manager obdrží zprávu, která povolit MSI na virtuálním počítači.
2. Azure Resource Manager vytvoří objekt služby ve službě Azure AD reprezentuje identitu virtuálního počítače. Objekt služby se vytvoří v klientovi Azure AD, který je důvěryhodný pro toto předplatné.
3. Azure Resource Manager nakonfiguruje Podrobnosti objektu služby v rozšíření virtuálního počítače MSI virtuálního počítače.  Tento krok zahrnuje konfiguraci ID klienta a certifikát používaný službou rozšíření k získání přístupových tokenů z Azure AD.
4. Teď, když se označuje identity instanční objekt virtuálního počítače, můžete udělit přístup k prostředkům Azure.  Například pokud váš kód potřebuje volat Azure Resource Manager, pak by přiřadíte instanční objekt Virtuálního počítače roli odpovídající pomocí řízení přístupu na základě Role (RBAC) ve službě Azure AD.  Pokud váš kód potřebuje volat Key Vault, by udělit přístup kódu k určitého tajného klíče nebo klíče v Key Vault.
5. Váš kód spuštěný ve virtuálním počítači požadavky token z místní koncový bod, který je hostitelem rozšíření virtuálního počítače MSI: http://localhost:50342/oauth2/token.  Parametr prostředku určuje službu, na kterou je odesláno token. Například pokud chcete, aby váš kód pro ověření do Azure Resource Manageru, byste použili prostředků = https://management.azure.com/.
6. Rozšíření virtuálního počítače MSI používá jeho ID konfigurovaného klienta a certifikát k vyžádání tokenu přístupu z Azure AD.  Azure AD vrátí přístupový token JSON Web Token (JWT).
7. Váš kód odešle přístupového tokenu pro volání na službu, která podporuje ověřování Azure AD.

Každý služba Azure, která podporuje identita spravované služby má svou vlastní metoda kódu získat přístupový token. Podívejte se na kurzy pro každou službu a zjistěte, konkrétní metody k získání tokenu.

## <a name="try-managed-service-identity"></a>Zkuste identita spravované služby

Zkuste identita spravované služby kurzu se dozvíte začátku do konce scénáře pro přístup k různými prostředky Azure:
<br><br>
| Z prostředků povoleno MSI | Získáte informace o těchto tématech: |
| ------- | -------- |
| Virtuální počítač Azure (Windows) | [Přístup k Azure Data Lake Store s Windows virtuální počítač identita spravované služby](msi-tutorial-windows-vm-access-datalake.md) |
|                    | [Přístup k Azure Resource Manager s Windows virtuální počítač identita spravované služby](msi-tutorial-windows-vm-access-arm.md) |
|                    | [Přístup k Azure SQL s Windows virtuální počítač identita spravované služby](msi-tutorial-windows-vm-access-sql.md) |
|                    | [Přístup k úložišti Azure přes přístupový klíč se virtuálních počítačů spravovaných identitu služby Windows](msi-tutorial-windows-vm-access-storage.md) |
|                    | [Přístup k Azure úložiště přes SAS s Windows virtuální počítač identita spravované služby](msi-tutorial-windows-vm-access-storage-sas.md) |
|                    | [Přístup k prostředku mimo Azure AD s Azure Key Vault a identita spravované služby aplikace systému Windows virtuálního počítače](msi-tutorial-windows-vm-access-nonaad.md) |
| Virtuální počítač Azure (Linux)   | [Přístup k Azure Data Lake Store s virtuálního počítače s Linuxem identita spravované služby](msi-tutorial-linux-vm-access-datalake.md) |
|                    | [Správce přístupu k prostředku Azure s virtuálního počítače s Linuxem identita spravované služby](msi-tutorial-linux-vm-access-arm.md) |
|                    | [Přístup k úložišti Azure přes přístupový klíč s identitou služby spravované Linux virtuálního počítače](msi-tutorial-linux-vm-access-storage.md) |
|                    | [Přístup k Azure úložiště přes SAS s virtuálního počítače s Linuxem identita spravované služby](msi-tutorial-linux-vm-access-storage-sas.md) |
|                    | [Přístup k prostředku mimo Azure AD s Azure Key Vault a identita služby spravovaných virtuálních počítačů Linux](msi-tutorial-linux-vm-access-nonaad.md) |
| Azure App Service  | [Identita spravované služby pomocí služby Azure App Service nebo Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Azure – funkce     | [Identita spravované služby pomocí služby Azure App Service nebo Azure Functions](/azure/app-service/app-service-managed-service-identity) |
| Azure Service Bus  | [Identita spravované služby pomocí služby Azure Service Bus](../service-bus-messaging/service-bus-managed-service-identity.md) |
| Azure Event Hubs   | [Identita spravované služby pomocí služby Azure Event Hubs](../event-hubs/event-hubs-managed-service-identity.md) |

## <a name="which-azure-services-support-managed-service-identity"></a>Které služby Azure podporuje identita spravované služby?

Služby Azure, které podporují spravované identit služby slouží k ověření služby, které podporují ověřování Azure AD MSI.  Probíhá integrace MSI a Azure AD ověřování přes Azure.  Zkontrolujte zpět často aktualizací.

### <a name="azure-services-that-support-managed-service-identity"></a>Služby Azure, které podporují identita spravované služby

Následující služby Azure podporují identita spravované služby.

| Služba | Status | Datum | Konfigurace | Získání tokenu |
| ------- | ------ | ---- | --------- | ----------- |
| Azure Virtual Machines | Preview | 2017 září | [portál Azure Portal](msi-qs-configure-portal-windows-vm.md)<br>[PowerShell](msi-qs-configure-powershell-windows-vm.md)<br>[Azure CLI](msi-qs-configure-cli-windows-vm.md)<br>[Šablony Azure Resource Manageru](msi-qs-configure-template-windows-vm.md) | [REST](msi-how-to-use-vm-msi-token.md#get-a-token-using-http)<br>[.NET](msi-how-to-use-vm-msi-token.md#get-a-token-using-c)<br>[Bash/Curl](msi-how-to-use-vm-msi-token.md#get-a-token-using-curl)<br>[Go](msi-how-to-use-vm-msi-token.md#get-a-token-using-go)<br>[PowerShell](msi-how-to-use-vm-msi-token.md#get-a-token-using-azure-powershell) |
| Azure App Service | Preview | 2017 září | [portál Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Šablona Azure Resource Manageru](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Functions | Preview | 2017 září | [portál Azure Portal](/azure/app-service/app-service-managed-service-identity#using-the-azure-portal)<br>[Šablona Azure Resource Manageru](/azure/app-service/app-service-managed-service-identity#using-an-azure-resource-manager-template) | [.NET](/azure/app-service/app-service-managed-service-identity#asal)<br>[REST](/azure/app-service/app-service-managed-service-identity#using-the-rest-protocol) |
| Azure Data Factory V2 | Preview | 2017 listopadu | [portál Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity)<br>[PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-powershell)<br>[REST](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-rest-api)<br>[Sada SDK](~/articles/data-factory/data-factory-service-identity.md#generate-service-identity-using-sdk) |

### <a name="azure-services-that-support-azure-ad-authentication"></a>Azure services, ověření podpory Azure AD

Následující služby podporují ověřování Azure AD a byly testovány s klienta služby, které používají identita spravované služby.

| Služba | ID prostředku | Status | Datum | Přiřadit přístup pro |
| ------- | ----------- | ------ | ---- | ------------- |
| Azure Resource Manager | https://management.azure.com | Dostupné | 2017 září | [portál Azure Portal](msi-howto-assign-access-portal.md) <br>[PowerShell](msi-howto-assign-access-powershell.md) <br>[Azure CLI](msi-howto-assign-access-CLI.md) |
| Azure Key Vault | https://vault.azure.net | Dostupné | 2017 září | |
| Azure Data Lake | https://datalake.azure.net | Dostupné | 2017 září | |
| Azure SQL | https://database.windows.net | Dostupné | Říjen 2017 | |
| Azure Event Hubs | https://eventhubs.azure.net | Dostupné | 2017 prosinec | |
| Azure Service Bus | https://servicebus.azure.net | Dostupné | 2017 prosinec | |

## <a name="how-much-does-managed-service-identity-cost"></a>Kolik identita spravované služby stojí?

Identita spravované služby se dodává s Azure Active Directory volná, což je výchozí nastavení pro předplatná Azure.  Není k dispozici bez dalších nákladů spravované identitu služby.

## <a name="support-and-feedback"></a>Podpora a zpětná vazba

Rádi od vás uslyšíme!

* Postupy: dotazy posílejte na Stack Overflow ke značce [azure msi](http://stackoverflow.com/questions/tagged/azure-msi).
* Zajistěte, aby funkce žádostí nebo na váš názor [fóru pro zpětnou vazbu Azure AD pro vývojáře](https://feedback.azure.com/forums/169401-azure-active-directory/category/164757-developer-experiences).






