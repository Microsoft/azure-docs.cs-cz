---
title: Exportovat protokol aktivit Azure
description: Exportujte protokol aktivit Azure do úložiště k archivaci nebo Azure Event Hubs pro export mimo Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 12c750f96b8852cdd6a6039ebfa750c2ee792a6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80396719"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Export protokolu aktivit Azure do úložiště nebo do Azure Event Hubs

> [!IMPORTANT]
> Metoda pro odeslání protokolu aktivit Azure pro Azure Storage a Azure Event Hubs se změnila na [nastavení diagnostiky](diagnostic-settings.md). Tento článek popisuje starší metodu, která se v procesu již nepoužívá. Podrobnější informace najdete v článku aktualizace pro [shromažďování a analýzu protokolu aktivit Azure v Azure monitor](activity-log-collect.md) .


[Protokol aktivit Azure](platform-logs-overview.md) poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo ve vašem předplatném Azure. Kromě zobrazení protokolu aktivit v Azure Portal nebo jeho zkopírování do pracovního prostoru Log Analytics, kde se dá analyzovat s jinými daty shromažďovanými Azure Monitor můžete vytvořit profil protokolu pro archivaci protokolu aktivit do účtu služby Azure Storage nebo jeho streamování do centra událostí.

## <a name="archive-activity-log"></a>Protokol aktivit archivu
Archivace protokolu aktivit do účtu úložiště je užitečná v případě, že byste chtěli uchovávat data protokolu déle než 90 dní (s úplnou kontrolou nad zásadami uchovávání informací) pro audit, statickou analýzu nebo zálohování. Pokud potřebujete události uchovávat jenom 90 dní nebo méně, nemusíte v účtu úložiště nastavovat archivaci, protože události protokolu aktivit se uchovávají na platformě Azure po dobu 90 dnů.

## <a name="stream-activity-log-to-event-hub"></a>Streamování protokolu aktivit do centra událostí
[Azure Event Hubs](/azure/event-hubs/) je platforma pro streamování dat a služba pro příjem událostí, která může přijímat a zpracovávat miliony událostí za sekundu. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Možnosti streamování pro protokol aktivit můžete použít dvěma způsoby:
* **Streamování do systémů protokolování a telemetrie třetích stran**: v průběhu času se streamování služby Azure Event Hubs stane mechanismem pro přesměrování vaší aktivity do řešení systémů Siem a Log Analytics třetích stran.
* **Sestavení vlastní telemetrie a**rozhraní pro protokolování: Pokud už máte vlastní platformu telemetrie nebo uvažujete o jejím sestavování, je vysoce škálovatelná verze Event Hubs pro publikování a odběr, která umožňuje flexibilní ingestování protokolu aktivit.

## <a name="prerequisites"></a>Požadavky

### <a name="storage-account"></a>Účet úložiště
Pokud budete protokol aktivit archivovat, musíte si [vytvořit účet úložiště](../../storage/common/storage-account-create.md) , pokud ho ještě nemáte. Neměli byste používat existující účet úložiště, který obsahuje jiná, nemonitorovaná data, která jsou v něm uložená, abyste mohli lépe řídit přístup k datům monitorování. Pokud i přesto archivujte protokoly a metriky do účtu úložiště, můžete použít stejný účet úložiště, abyste zachovali všechna data monitorování v centrálním umístění.

Účet úložiště nemusí být ve stejném předplatném jako odběr, který vydává protokoly, pokud uživatel, který nakonfiguruje nastavení, má odpovídající přístup RBAC k oběma předplatným. 

> [!TIP]
> Další informace najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) pro poskytování přístupu k účtu úložiště za zabezpečenou virtuální sítí.

### <a name="event-hubs"></a>Event Hubs
Pokud odesíláte protokol aktivit do centra událostí, budete muset [vytvořit centrum událostí](../../event-hubs/event-hubs-create.md) , pokud ho ještě nemáte. Pokud jste do tohoto oboru názvů Event Hubs dříve zasílaly události protokolu aktivit, pak se toto centrum událostí znovu použije.

Zásady sdíleného přístupu definují oprávnění, která má mechanismus streamování. Streamování do Event Hubs vyžaduje oprávnění spravovat, odesílat a naslouchat. Zásady sdíleného přístupu pro obor názvů Event Hubs můžete vytvořit nebo upravit v Azure Portal na kartě konfigurovat pro obor názvů Event Hubs.

Pokud chcete profil protokolu aktivit aktualizovat tak, aby zahrnoval streamování, musíte mít pro toto Event Hubs autorizační pravidlo oprávnění ListKey. Obor názvů Event Hubs nemusí být ve stejném předplatném, jako je předplatné, které vysílá protokoly, pokud uživatel, který nastavení nakonfiguruje, má odpovídající přístup RBAC k oběma předplatným a oba odběry jsou ve stejném tenantovi AAD.

Streamujte protokol aktivit do centra událostí [vytvořením profilu protokolu](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Vytvoření profilu protokolu
Můžete definovat způsob exportu protokolu aktivit Azure pomocí **profilu protokolu**. Každé předplatné Azure může mít jenom jeden profil protokolu. Tato nastavení se dají nakonfigurovat pomocí možnosti **exportovat** v okně Protokol aktivit na portálu. Dá se taky nakonfigurovat programově [pomocí Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), rutin PowerShellu nebo rozhraní příkazového řádku.

Profil protokolu definuje následující.

**Kam se má protokol aktivit odeslat.** V současné době jsou dostupné možnosti účet úložiště nebo Event Hubs.

**Které kategorie událostí by se měly odeslat.** Význam *kategorie* v profilech protokolů a událostech protokolu aktivit se liší. V profilu protokolu *kategorie* představuje typ operace (zápis, odstranit, akce). *Kategorie*"* vlastnost" v události protokolu aktivit představuje zdroj nebo typ události (například Správa, ServiceHealth a výstraha).

**Které oblasti (umístění) by měly být exportovány.** Měli byste zahrnout všechna umístění, protože mnoho událostí v protokolu aktivit je globální události.

**Jak dlouho má být protokol aktivit uchováván v účtu úložiště.** Nastavení doby uchovávání na 0 dní znamená, že se protokoly uchovávají trvale. V opačném případě může být hodnota libovolný počet dní mezi 1 a 365.

Pokud jsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázané, pak zásady uchovávání nemají žádný vliv. Zásady uchovávání informací se aplikují za den, takže na konci dne (UTC) se odhlásí od dne, který je teď mimo zásadu uchovávání informací. Například pokud jste měli zásady uchovávání informací jeden den, na začátku dne v dnešní době budou odstraněny protokoly od dne před včera. Proces odstranění začíná o půlnoci UTC, ale Všimněte si, že může trvat až 24 hodin, než se protokoly odstraní z vašeho účtu úložiště.


> [!IMPORTANT]
> Pokud poskytovatel prostředků Microsoft. Insights není zaregistrovaný, může se vám při vytváření profilu protokolu zobrazovat chyba. Pokud chcete tohoto poskytovatele zaregistrovat, přečtěte si téma [poskytovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md) .


### <a name="create-log-profile-using-the-azure-portal"></a>Vytvořit profil protokolu pomocí Azure Portal

Vytvořte nebo upravte profil protokolu s možností **exportovat do centra událostí** v Azure Portal.

1. V nabídce **Azure monitor** v Azure Portal vyberte **Protokol aktivit**.
3. Klikněte na **Nastavení diagnostiky**.

   ![Nastavení diagnostiky](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klikněte na fialový banner pro starší verze prostředí.

    ![Starší verze prostředí](media/diagnostic-settings-subscription/legacy-experience.png)

3. V okně, které se zobrazí, zadejte následující:
   * Oblasti s událostmi k exportu. Měli byste vybrat všechny oblasti, abyste se ujistili, že nedošlo ke klíčovým událostem, protože protokol aktivit je globální (neregionální) protokol, a takže většina událostí nemá přidruženou oblast.
   * Pokud chcete zapisovat do účtu úložiště:
       * Účet úložiště, na který byste chtěli ukládat události.
       * Počet dní, po které mají být tyto události v úložišti uchovávány. Nastavení 0 dnů uchová protokoly navždy.
   * Pokud chcete zapisovat do centra událostí:
       * Obor názvů Service Bus, ve kterém se má vytvořit centrum událostí pro streamování těchto událostí.

     ![Okno Exportovat protokol aktivit](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Kliknutím na **Uložit** tato nastavení uložte. Nastavení se okamžitě použijí pro vaše předplatné.


### <a name="configure-log-profile-using-powershell"></a>Konfigurace profilu protokolu pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud profil protokolu již existuje, musíte nejprve odebrat existující profil protokolu a pak vytvořit nový.

1. Použijte `Get-AzLogProfile` k určení, jestli profil protokolu existuje.  Pokud profil protokolu existuje, poznamenejte si jeho vlastnost *Name* .

1. Pomocí `Remove-AzLogProfile` odeberte profil protokolu pomocí hodnoty z vlastnosti *název* .

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Použijte `Add-AzLogProfile` k vytvoření nového profilu protokolu:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Vlastnost | Požaduje se | Popis |
    | --- | --- | --- |
    | Název |Ano |Název vašeho profilu protokolu. |
    | StorageAccountId |Ne |ID prostředku účtu úložiště, do kterého se má ukládat protokol aktivit |
    | serviceBusRuleId |Ne |Service Bus ID pravidla pro Service Bus oboru názvů, ve kterém chcete vytvořit centra událostí. Toto je řetězec ve formátu: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Umístění |Ano |Čárkami oddělený seznam oblastí, pro které chcete shromažďovat události protokolu aktivit. |
    | RetentionInDays |Ano |Počet dní, po které se mají události uchovávat v účtu úložiště v rozmezí od 1 do 365. Hodnota nula ukládá protokoly po neomezenou dobu. |
    | Kategorie |Ne |Čárkami oddělený seznam kategorií událostí, které se mají shromáždit. Možné hodnoty jsou _Write_, _Delete_a _Action_. |

### <a name="example-script"></a>Ukázkový skript
Následuje ukázkový skript prostředí PowerShell pro vytvoření profilu protokolu aktivit, který zapisuje protokol aktivit do účtu úložiště i centra událostí.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurace profilu protokolu pomocí Azure CLI

Pokud profil protokolu již existuje, musíte nejprve odebrat existující profil protokolu a pak vytvořit nový profil protokolu.

1. Použijte `az monitor log-profiles list` k určení, jestli profil protokolu existuje.
2. Pomocí `az monitor log-profiles delete --name "<log profile name>` odeberte profil protokolu pomocí hodnoty z vlastnosti *název* .
3. Použijte `az monitor log-profiles create` k vytvoření nového profilu protokolu:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Vlastnost | Požaduje se | Popis |
    | --- | --- | --- |
    | jméno |Ano |Název vašeho profilu protokolu. |
    | úložiště – ID účtu |Ano |ID prostředku účtu úložiště, do kterého se mají ukládat protokoly aktivit |
    | polohy |Ano |Mezerou oddělený seznam oblastí, pro které chcete shromažďovat události protokolu aktivit. Seznam všech oblastí pro vaše předplatné můžete zobrazit pomocí `az account list-locations --query [].name`. |
    | denní |Ano |Počet dní, po které se mají uchovávat události v rozmezí od 1 do 365. Hodnota nula bude ukládat protokoly po neomezenou dobu (navždy).  Je-li nastavena hodnota nula, parametr Enabled by měl být nastaven na hodnotu false. |
    |enabled | Ano |True nebo False  Slouží k povolení nebo zakázání zásad uchovávání informací.  Pokud je hodnota true, parametr Days musí být hodnota větší než 0.
    | categories |Ano |Prostor – seznam kategorií událostí, které mají být shromážděny. Možné hodnoty jsou Write, DELETE a Action. |



## <a name="next-steps"></a>Další kroky

* [Další informace o protokolu aktivit](../../azure-resource-manager/management/view-activity-logs.md)
* [Shromáždění protokolu aktivit do protokolů Azure Monitor](activity-log-collect.md)
