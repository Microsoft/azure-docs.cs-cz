---
title: Export protokolu aktivit Azure
description: Exportujte protokol aktivit Azure do úložiště pro archivaci nebo Centra událostí Azure pro export mimo Azure.
author: bwren
services: azure-monitor
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 12c750f96b8852cdd6a6039ebfa750c2ee792a6b
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396719"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Export protokolu aktivit Azure do úložiště nebo Centra událostí Azure

> [!IMPORTANT]
> Metoda odesílání protokolu aktivit Azure do Azure Storage a Azure Event Hubs se změnila na [diagnostická nastavení](diagnostic-settings.md). Tento článek popisuje starší metodu, která je v procesu zastaralá. Na webu Update to [Collect and analyze Azure Activity log in Azure Monitor](activity-log-collect.md) for a comparison.


[Protokol aktivit Azure](platform-logs-overview.md) poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo ve vašem předplatném Azure. In addition to viewing the Activity log in the Azure portal or copying it to a Log Analytics workspace where it can be analyzed with other data collected by Azure Monitor, you can create a log profile to archive the Activity log to an Azure storage account or stream it to an Event Hub.

## <a name="archive-activity-log"></a>Protokol aktivit archivu
Archivace protokolu aktivit na účet úložiště je užitečná, pokud chcete uchovávat data protokolu déle než 90 dní (s plnou kontrolou nad zásadami uchovávání informací) pro audit, statickou analýzu nebo zálohování. Pokud potřebujete zachovat události po dobu 90 dnů nebo méně, nemusíte nastavit archivaci na účet úložiště, protože události protokolu aktivit jsou zachovány v platformě Azure po dobu 90 dnů.

## <a name="stream-activity-log-to-event-hub"></a>Streamovat protokol aktivit do centra událostí
[Azure Event Hubs](/azure/event-hubs/) je platforma pro streamování dat a služba příjmu událostí, která může přijímat a zpracovávat miliony událostí za sekundu. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Dvě možnosti streamování pro protokol aktivit jsou následující:
* **Streamování do systémů protokolování a telemetrie třetích stran**: V průběhu času se streamování azure event hubů stane mechanismem pro přenos vašeho protokolu aktivit do řešení siem třetích stran a řešení pro analýzu protokolů.
* **Vytvořte vlastní platformu telemetrie a protokolování**: Pokud už máte vlastní telemetrickou platformu nebo uvažujete o vytvoření, vysoce škálovatelná povaha publikování a odběru centra událostí umožňuje flexibilně ingestovat protokol aktivit.

## <a name="prerequisites"></a>Požadavky

### <a name="storage-account"></a>Účet úložiště
Pokud archivujete protokol aktivit, musíte [si vytvořit účet úložiště,](../../storage/common/storage-account-create.md) pokud ho ještě nemáte. Neměli byste používat existující účet úložiště, který má jiné, non-monitoring data uložená v něm, takže můžete lépe řídit přístup k datům monitorování. Pokud také archivujete protokoly a metriky do účtu úložiště, můžete použít stejný účet úložiště, abyste uchovávají všechna data monitorování v centrálním umístění.

Účet úložiště nemusí být ve stejném předplatném jako protokoly vyzařující odběr, pokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC k oběma předplatným. 

> [!TIP]
> Na [webu Konfigurace firewallů azure storage a virtuálních sítí,](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) kde najdete přístup k účtu úložiště za zabezpečenou virtuální sítí.

### <a name="event-hubs"></a>Event Hubs
Pokud odesíláte protokol aktivit do centra událostí, musíte [vytvořit centrum událostí,](../../event-hubs/event-hubs-create.md) pokud ho ještě nemáte. Pokud jste dříve streamovali události protokolu aktivit do tohoto oboru názvů Centra událostí, bude toto centrum událostí znovu použito.

Zásady sdíleného přístupu definují oprávnění, která má mechanismus streamování. Streamování do centra událostí vyžaduje oprávnění Ke správě, odesílání a naslouchání. Můžete vytvořit nebo upravit zásady sdíleného přístupu pro obor názvů Event Hubs na webu Azure Portal na kartě Konfigurace pro obor názvů Event Hubs.

Chcete-li aktualizovat profil protokolu aktivit tak, aby zahrnoval streamování, musíte mít oprávnění ListKey k tomuto autorizačnímu pravidlu centra událostí. Obor názvů Event Hubs nemusí být ve stejném předplatném jako předplatné, které vyzařuje protokoly, pokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC k oběma předplatným a obě předplatná jsou ve stejném tenantovi AAD.

Streamujte protokol aktivit do centra událostí [vytvořením profilu protokolu](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Vytvoření profilu protokolu
Můžete definovat, jak se exportuje váš protokol aktivit Azure pomocí **profilu protokolu**. Každé předplatné Azure může mít jenom jeden profil protokolu. Tato nastavení lze nakonfigurovat pomocí možnosti **Exportovat** v okně Protokol aktivit na portálu. Můžete je také nakonfigurovat programově [pomocí rozhraní AZURE Monitor REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx), rutin prostředí PowerShell nebo CLI.

Profil protokolu definuje následující.

**Kde by měl být odeslán protokol aktivit.** V současné době jsou k dispozici dostupné možnosti, aby účet úložiště nebo centra událostí.

**Které kategorie událostí by měly být odeslány.** Význam *kategorie* v profilech protokolu a události protokolu aktivit se liší. V profilu protokolu *kategorie* představuje typ operace (Zápis, Odstranit, Akce). V protokolu aktivit událost *kategorie*"* vlastnost představuje zdroj nebo typ události (například správa, ServiceHealth a výstraha).

**Které oblasti (umístění) by měly být exportovány.** Měli byste zahrnout všechna umístění, protože mnoho událostí v protokolu aktivit jsou globální události.

**Jak dlouho by měl být protokol aktivit zachován v účtu úložiště.** Nastavení doby uchovávání na 0 dní znamená, že se protokoly uchovávají trvale. V opačném případě může být hodnota libovolný počet dní mezi 1 a 365.

Pokud jsou nastaveny zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázáno, pak zásady uchovávání informací nemají žádný vliv. Zásady uchovávání informací se používají za den, takže na konci dne (UTC) protokoly ze dne, který je nyní mimo zásady uchovávání informací jsou odstraněny. Například pokud jste měli zásady uchovávání informací jeden den, na začátku dne dnes protokoly z předevčírem budou odstraněny. Proces odstranění začíná o půlnoci UTC, ale všimněte si, že může trvat až 24 hodin pro protokoly, které mají být odstraněny z vašeho účtu úložiště.


> [!IMPORTANT]
> Pokud není poskytovatel prostředků Microsoft.Insights zaregistrován, může se při vytváření profilu protokolu zobrazit chyba. Viz [Zprostředkovatelé a typy prostředků Azure](../../azure-resource-manager/management/resource-providers-and-types.md) k registraci tohoto zprostředkovatele.


### <a name="create-log-profile-using-the-azure-portal"></a>Vytvoření profilu protokolu pomocí portálu Azure

Vytvořte nebo upravte profil protokolu pomocí možnosti **Exportovat do centra událostí** na webu Azure Portal.

1. Z nabídky **Azure Monitor** na webu Azure Portal vyberte **protokol aktivit**.
3. Klikněte na **Nastavení diagnostiky**.

   ![Nastavení diagnostiky](media/diagnostic-settings-subscription/diagnostic-settings.png)

4. Klikněte na fialový banner pro starší prostředí.

    ![Starší verze prostředí](media/diagnostic-settings-subscription/legacy-experience.png)

3. V noži, která se zobrazí, zadejte následující:
   * Oblasti s událostmi k exportu. Měli byste vybrat všechny oblasti, abyste zajistili, že nezmeškáte klíčové události, protože protokol aktivit je globální (neregionální) protokol, a proto většina událostí nemá k nim přidruženou oblast.
   * Pokud chcete zapisovat do účtu úložiště:
       * Účet úložiště, do kterého chcete uložit události.
       * Počet dní, po které chcete zachovat tyto události v úložišti. Nastavení 0 dní uchovává protokoly navždy.
   * Pokud chcete zapisovat do centra událostí:
       * Obor názvů služby Service Bus, ve kterém chcete vytvořit centrum událostí pro streamování těchto událostí.

     ![Okno Exportovat protokol aktivit](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Chcete-li tato nastavení uložit, klepněte na tlačítko **Uložit.** Nastavení se okamžitě použijí pro vaše předplatné.


### <a name="configure-log-profile-using-powershell"></a>Konfigurace profilu protokolu pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud profil protokolu již existuje, je třeba nejprve odebrat existující profil protokolu a potom vytvořit nový.

1. Slouží `Get-AzLogProfile` k identifikaci, zda existuje profil protokolu.  Pokud profil protokolu existuje, poznamenejte si vlastnost *name.*

1. Slouží `Remove-AzLogProfile` k odebrání profilu protokolu pomocí hodnoty z vlastnosti *name.*

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Slouží `Add-AzLogProfile` k vytvoření nového profilu protokolu:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Vlastnost | Požaduje se | Popis |
    | --- | --- | --- |
    | Name (Název) |Ano |Název profilu protokolu. |
    | Id účtu úložiště |Ne |ID prostředku účtu úložiště, do kterého by měl být uložen protokol aktivit. |
    | serviceBusRuleId |Ne |ID pravidla sběrnice pro obor názvů Service Bus, ve které chcete mít vytvořena centra událostí. Jedná se o řetězec `{service bus resource ID}/authorizationrules/{key name}`s formátem: . |
    | Umístění |Ano |Seznam oblastí oddělených čárkami, pro které chcete shromažďovat události protokolu aktivit. |
    | RetenceInDays |Ano |Počet dní, po které by měly být události uchovávány v účtu úložiště, mezi 1 a 365. Hodnota nula ukládá protokoly neomezeně dlouho. |
    | Kategorie |Ne |Seznam kategorií událostí oddělených čárkami, které by měly být shromažďovány. Možné hodnoty jsou _Zápis_, _Odstranit_a _Akce_. |

### <a name="example-script"></a>Ukázkový skript
Následuje ukázkový skript prostředí PowerShell pro vytvoření profilu protokolu, který zapisuje protokol aktivit do účtu úložiště i do centra událostí.

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


### <a name="configure-log-profile-using-azure-cli"></a>Konfigurace profilu protokolu pomocí rozhraní příkazového příkazového příkazu Azure

Pokud profil protokolu již existuje, je třeba nejprve odebrat existující profil protokolu a potom vytvořit nový profil protokolu.

1. Slouží `az monitor log-profiles list` k identifikaci, zda existuje profil protokolu.
2. Slouží `az monitor log-profiles delete --name "<log profile name>` k odebrání profilu protokolu pomocí hodnoty z vlastnosti *name.*
3. Slouží `az monitor log-profiles create` k vytvoření nového profilu protokolu:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Vlastnost | Požaduje se | Popis |
    | --- | --- | --- |
    | jméno |Ano |Název profilu protokolu. |
    | id účtu úložiště |Ano |ID prostředku účtu úložiště, do kterého mají být uloženy protokoly aktivit. |
    | Umístění |Ano |Prostorově oddělený seznam oblastí, pro které chcete shromažďovat události protokolu aktivit. Pomocí aplikace můžete zobrazit seznam všech `az account list-locations --query [].name`oblastí předplatného. |
    | Dní |Ano |Počet dní, po které by měly být události zachovány, mezi 1 a 365. Hodnota nula bude ukládat protokoly neomezeně dlouho (navždy).  Pokud nula, pak by měl být povolený parametr nastaven na false. |
    |enabled | Ano |True nebo False  Slouží k povolení nebo zakázání zásad uchovávání informací.  Pokud True, pak days parametr musí být hodnota větší než 0.
    | categories |Ano |Prostorově oddělený seznam kategorií událostí, které by měly být shromažďovány. Možné hodnoty jsou Zápis, Odstranit a Akce. |



## <a name="next-steps"></a>Další kroky

* [Další informace o protokolu aktivit](../../azure-resource-manager/management/view-activity-logs.md)
* [Shromažďování protokolů aktivit do protokolů monitorování Azure](activity-log-collect.md)
