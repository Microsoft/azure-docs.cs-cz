---
title: Protokolování spravovaného HSM Azure
description: Tento kurz vám umožní začít se spravovaným protokolováním HSM.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 22abd38ead1257b49eeae98acfcd74349f563811
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91000663"
---
# <a name="managed-hsm-logging"></a>Spravované protokolování HSM 

Po vytvoření jednoho nebo více spravovaných HSM budete pravděpodobně chtít monitorovat, jak a kdy k HSMss máte pøístup a kým. To můžete provést tak, že povolíte protokolování, které ukládá informace v účtu úložiště Azure, který poskytnete. Pro zadaný účet úložiště se automaticky vytvoří nový kontejner s názvem **Insights-logs-auditevent** . Stejný účet úložiště můžete použít pro shromažďování protokolů pro více spravovaných HSM.

Po spravované operaci HSM máte přístup k informacím o protokolování 10 minut (nejvíce). Ve většině případů to bude rychlejší.  Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.

Tento kurz vám umožní začít se spravovaným protokolováním HSM. Vytvoříte účet úložiště, povolíte protokolování a interpretují shromážděné informace protokolu.  

> [!NOTE]
> Tento kurz neobsahuje pokyny, jak vytvořit spravované HSM nebo klíče. Tento článek poskytuje pokyny pro rozhraní příkazového řádku Azure CLI pro aktualizaci protokolování diagnostiky.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku musíte mít následující položky:

* Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Verze Azure CLI 2.12.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).
* Spravovaný modul HSM v rámci vašeho předplatného. Informace najdete v tématu [rychlý Start: zřízení a aktivace spravovaného modulu HSM pomocí Azure CLI](quick-create-cli.md) , který umožňuje zřídit a aktivovat spravovaný modul HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Připojení k předplatnému služby Azure

Prvním krokem při nastavení protokolování klíčů je Ukázat Azure CLI na spravovaný modul HSM, který chcete protokolovat.

```azurecli-interactive
az login
```

Další informace o možnostech přihlášení prostřednictvím rozhraní příkazového řádku si můžete prohlédnout v části [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true) .

Možná budete muset zadat předplatné, které jste použili k vytvoření spravovaného modulu HSM. Zadáním následujícího příkazu zobrazíte odběry pro váš účet:

## <a name="identify-the-managed-hsm-and-storage-account"></a>Identifikace spravovaného modulu HSM a účtu úložiště

```azurecli-interactive
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name ContosoMHSMLogs --query id -o tsv)
```

## <a name="enable-logging"></a>Povolit protokolování

Pokud chcete povolit protokolování pro spravovaný modul HSM, použijte příkaz **AZ monitor Diagnostic-Settings Create** spolu s proměnnými, které jsme vytvořili pro nový účet úložiště a spravovaný modul HSM. Také nastavíme příznak **-Enabled** na **$true** a nastavíte kategorii na **AuditEvent** (jediná kategorie pro spravované protokolování HSM):

Tento výstup potvrdí, že protokolování je teď povolené pro váš spravovaný modul HSM, a uloží informace do svého účtu úložiště.

Volitelně můžete pro své protokoly nastavit zásady uchovávání informací tak, aby se starší protokoly automaticky odstranily. Nastavte například zásady uchovávání informací nastavením příznaku **-RetentionEnabled** na **$true**a nastavte parametr **-RetentionInDays** na **90** tak, aby se automaticky odstranily protokoly starší než 90 dní.

```azurecli-interactive
az monitor diagnostic-settings create --name ContosoMHSM-Diagnostics --resource $hsmresource --logs '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource
```

Co je protokolováno:

* Všechny požadavky ověřených REST API, včetně neúspěšných žádostí v důsledku oprávnění přístupu, systémových chyb nebo chybných požadavků.
* Operace samotného spravovaného HSM, včetně vytváření, odstraňování a aktualizace atributů, jako jsou například značky.
* Operace související s doménou zabezpečení, jako je například inicializace & stažení, inicializace obnovení, nahrání
* Úplné operace zálohování HSM, obnovení a selektivního obnovení
* Operace s klíči, včetně:
  * Vytváření, úpravy nebo odstraňování klíčů.
  * Podepisování, ověřování, šifrování, dešifrování, zabalení a rozbalení klíčů a výpis klíčů.
  * Zálohování klíčů, obnovení, vyprázdnění
* Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Příklady jsou požadavky, které nemají nosný token, které jsou poškozené nebo jejichž platnost vypršela, nebo které mají neplatný token.  

## <a name="access-your-logs"></a>Přístup k vašim protokolům

Spravované protokoly HSM se ukládají do kontejneru **Insights-logs-auditevent** v účtu úložiště, který jste zadali. Chcete-li zobrazit protokoly, je nutné stáhnout objekty blob. Informace o Azure Storage najdete v tématu [Vytvoření, stažení a výpis objektů BLOB pomocí Azure CLI](../../storage/blobs/storage-quickstart-blobs-cli.md).

Jednotlivé objekty blob jsou uložené jako text formátované jako JSON. Pojďme se podívat na příklad položky protokolu. Následující příklad ukazuje položku protokolu, pokud je žádost o vytvoření úplné zálohy odeslána do spravovaného modulu HSM.

```json
[
  {
    "TenantId": "766eaf62-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "time": "2020-08-31T19:52:39.763Z",
    "resourceId": "/SUBSCRIPTIONS/A1BA9AAA-xxxx-xxxx-xxxx-xxxxxxxxxxxx/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/MANAGEDHSMS/CONTOSOMHSM",
    "operationName": "BackupCreate",
    "operationVersion": "7.0",
    "category": "AuditEvent",
    "resultType": "Success",
    "properties": {
        "PoolType": "M-HSM",
        "sku_Family": "B",
        "sku_Name": "Standard_B1"
    },
    "durationMs": 488,
    "callerIpAddress": "X.X.X.X",
    "identity": "{\"claim\":{\"appid\":\"04b07795-xxxx-xxxx-xxxx-xxxxxxxxxxxx\",\"http_schemas_microsoft_com_identity\":{\"claims\":{\"objectidentifier\":\"b1c52bf0-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"}},\"http_schemas_xmlsoap_org_ws_2005_05_identity\":{\"claims\":{\"upn\":\"admin@contoso.com\"}}}}",
    "clientInfo": "azsdk-python-core/1.7.0 Python/3.8.2 (Linux-4.19.84-microsoft-standard-x86_64-with-glibc2.29) azsdk-python-azure-keyvault/7.2",
    "correlationId": "8806614c-ebc3-11ea-9e9b-00155db778ad",
    "subnetId": "(unknown)",
    "httpStatusCode": 202,
    "PoolName": "mhsmdemo",
    "requestUri": "https://ContosoMHSM.managedhsm.azure.net/backup",
    "resourceGroup": "ContosoResourceGroup",
    "resourceProvider": "MICROSOFT.KEYVAULT",
    "resource": "ContosoMHSM",
    "resourceType": "managedHSMs"
  }
]
```

V následující tabulce jsou uvedené názvy a popisy polí:

| Název pole | Popis |
| --- | --- |
| **TenantId** | ID tenanta Azure Active Directory předplatného, ve kterém se vytvořil spravovaný modul HSM |
| **interval** |Datum a čas ve standardu UTC. |
| **Prostředku** |ID prostředku Azure Resource Manager. U spravovaných protokolů HSM se jedná vždy o spravované ID prostředku HSM. |
| **operationName** |Název operace, jak popisuje následující tabulka. |
| **operationVersion** |Verze REST API požadovaná klientem |
| **kategorií** |Typ výsledku. U spravovaných protokolů HSM je **AuditEvent** jedinou dostupnou hodnotou. |
| **Hodnotu** |Výsledek žádosti o REST API |
| **vlastnosti** |Informace, které se liší v závislosti na operaci (**OperationName**)|
| **resultSignature** |Stav HTTP. |
| **resultDescription** |Další popis výsledku, je-li k dispozici. |
| **Trvání v MS** |Doba trvání obsloužení požadavku REST API v milisekundách. Nezahrnuje latenci sítě, takže čas naměřený na straně klienta se může lišit. |
| **callerIpAddress** |IP adresa klienta, který odeslal požadavek. |
| **ID** |Nepovinný identifikátor GUID, který může klient předat pro korelaci protokolů na straně klienta s protokoly na straně služby. |
| **odcizen** |Identita z tokenu, který byl předložen v žádosti REST API. Většinou se jedná o "uživatele" a "instanční objekt". |
| **requestUri** | Identifikátor URI žádosti o REST API |
| **Informace CLIENTINFO.** | 

## <a name="use-azure-monitor-logs"></a>Použití protokolů Azure Monitoru

K revizi spravovaných protokolů HSM **AuditEvent** můžete použít řešení Key Vault v protokolech Azure monitor. V protokolech Azure Monitor použijete dotazy protokolu k analýze dat a získání informací, které potřebujete. 

## <a name="next-steps"></a>Další kroky

- Seznamte se s [osvědčenými postupy](best-practices.md) pro zřizování a používání spravovaného modulu HSM.
- Informace o [tom, jak zálohovat a obnovit](backup-restore.md) spravovaný modul HSM
