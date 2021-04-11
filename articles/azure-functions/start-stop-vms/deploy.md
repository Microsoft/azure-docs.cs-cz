---
title: Nasazení spuštění/zastavení virtuálních počítačů v2 (Preview)
description: V tomto článku se dozvíte, jak nasadit funkci spustit nebo zastavit virtuální počítače v2 (Preview) pro vaše virtuální počítače Azure ve vašem předplatném Azure.
services: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.topic: conceptual
ms.openlocfilehash: 9ca808fffbd26c8837ad9a43447f60e99f89d922
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111730"
---
# <a name="deploy-startstop-vms-v2-preview"></a>Nasazení spuštění/zastavení virtuálních počítačů v2 (Preview)

Postupujte podle kroků v tomto tématu v části pořadí a nainstalujte funkci spustit/zastavit virtuální počítače v2 (Preview). Po dokončení procesu instalace nakonfigurujte plány tak, aby se přizpůsobily vašim požadavkům.

## <a name="deploy-feature"></a>Nasadit funkci

Nasazení se zahajuje [z organizace pro](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)spuštění nebo zastavení virtuálních počítačů verze 2 v GitHubu. Tato funkce je určená ke správě všech virtuálních počítačů ve vašem předplatném v rámci všech skupin prostředků z jednoho nasazení v rámci předplatného, a to na základě modelu operací nebo požadavků vaší organizace. Dá se taky nakonfigurovat k centrální správě virtuálních počítačů napříč několika předplatnými.

Pro zjednodušení správy a odebrání doporučujeme, abyste nasadili příkaz Spustit nebo zastavit virtuální počítače v2 (Preview) do vyhrazené skupiny prostředků.

> [!NOTE]
> V současné době tato verze Preview nepodporuje zadání existujícího účtu úložiště nebo prostředku Application Insights.

1. Otevřete prohlížeč a přejděte k [organizaci GitHubu](https://github.com/microsoft/startstopv2-deployments/blob/main/README.md)pro spuštění/zastavení virtuálních počítačů v2.
1. Vyberte možnost nasazení na základě cloudového prostředí Azure, ve kterém jsou vytvořené vaše virtuální počítače Azure. Tím se otevře stránka pro nasazení vlastní Azure Resource Manager v Azure Portal.
1. Pokud se zobrazí výzva, přihlaste se k [Azure Portal](https://portal.azure.com).
1. Zadejte tyto hodnoty:

    |Name |Hodnota |
    |-----|------|
    |Oblast |Vyberte oblast poblíž pro nové prostředky.|
    |Název skupiny prostředků |Zadejte název skupiny prostředků, který bude obsahovat jednotlivé prostředky pro virtuální počítače spustit nebo zastavit. |
    |Oblast skupiny prostředků |Zadejte oblast pro skupinu prostředků. Například **USA – střed**.|
    |Název Function App Azure |Zadejte název, který je platný v cestě URL. Název, který zadáte, bude ověřený, abyste se ujistili, že je v Azure Functions jedinečný. |
    |Název Application Insights |Zadejte název instance Application Insights, který bude obsahovat analýzu pro virtuální počítače spustit nebo zastavit. |
    |Oblast Application Insights |Zadejte oblast pro instanci Application Insights.|
    |Název účtu úložiště |Zadejte název účtu Azure Storage k uložení telemetrie spouštění a zastavování virtuálních počítačů. |
    |E-mailová adresa |Zadejte jednu nebo více e-mailových adres, pro které budou přijímána oznámení o stavu oddělená čárkou (,).|

    :::image type="content" source="media/deploy/deployment-template-details.png" alt-text="Spustit/zastavit konfiguraci nasazení šablony virtuálních počítačů":::

1. V dolní části stránky vyberte **zkontrolovat + vytvořit** .
1. Vyberte **vytvořit** a spusťte nasazení.
1. Stav nasazení zobrazíte tak, že nahoře na obrazovce vyberete ikonu zvonku (oznámení). Uvidíte, že probíhá **nasazení**. Počkejte na dokončení nasazení.
1. V podokně oznámení vyberte **Přejít ke skupině prostředků**. Zobrazí se podobná obrazovka:

    :::image type="content" source="media/deploy/deployment-results-resource-list.png" alt-text="Spustit nebo zastavit seznam prostředků nasazení šablon virtuálních počítačů":::

## <a name="enable-multiple-subscriptions"></a>Povolit více předplatných

Po dokončení nasazení spustit nebo zastavit proveďte následující kroky, aby bylo možné provést akci při spuštění nebo zastavení virtuálních počítačů v2 (Preview) v rámci více předplatných.

1. Zkopírujte hodnotu pro název Function App pro Azure, který jste zadali během nasazování.

1. Na portálu přejděte do svého sekundárního předplatného. Vyberte předplatné a pak vyberte **Access Control (IAM)** .

1. Vyberte **Přidat** a pak vyberte **Přidat přiřazení role**.

1. V rozevíracím seznamu **role** vyberte roli **Přispěvatel** .

1. Do pole pro **Výběr** zadejte název aplikace funkce Azure Functions. Ve výsledcích vyberte název funkce.

1. Vyberte **Uložit** a potvrďte provedené změny.

## <a name="configure-schedules-overview"></a>Přehled konfigurace plánů

Pokud chcete spravovat metodu automatizace pro řízení spouštění a zastavování virtuálních počítačů, nakonfigurujete jednu nebo více zahrnutých aplikací logiky na základě vašich požadavků.

- Akce plánovaného spuštění a zastavení jsou založené na plánu, který zadáte pro Azure Resource Manager a klasické virtuální počítače.**ststv2_vms_Scheduled_start** a **ststv2_vms_Scheduled_stop** nakonfigurovat naplánované spuštění a zastavení.

- Akce sekvencované-spuštění a zastavení jsou založené na plánu, který cílí na virtuální počítače s předem definovanými značkami Sequence. Podporovány jsou pouze dvě pojmenované značky – **sequencestart** a **sequencestop**. **ststv2_vms_Sequenced_start** a **ststv2_vms_Sequenced_stop** konfiguraci sekvencované spuštění a zastavení.

    > [!NOTE]
    > Tento scénář podporuje pouze Azure Resource Manager virtuálních počítačů.

- Autostop – Tato funkce se používá k provedení akce zastavení u Azure Resource Manager i klasických virtuálních počítačů na základě jejího využití procesoru. Může se také jednat o naplánované provedení *Akce*, která vytvoří výstrahy na virtuálních počítačích a na základě této podmínky se aktivuje výstraha k provedení akce zastavení.**ststv2_vms_AutoStop** nakonfiguruje funkci automatického zastavení.

Pokud potřebujete další plány, můžete duplikovat jednu z Logic Apps k dispozici pomocí možnosti **klonování** v Azure Portal.

:::image type="content" source="media/deploy/logic-apps-clone-option.png" alt-text="Výběr možnosti klonování pro duplikaci aplikace logiky":::

## <a name="scheduled-start-and-stop-scenario"></a>Scénář plánovaného spuštění a zastavení

Provedením následujících kroků nakonfigurujte naplánovanou akci spuštění a zastavení pro Azure Resource Manager a klasické virtuální počítače. Můžete například nakonfigurovat **ststv2_vms_Scheduled_start** plán tak, aby se do ráno spouštěl, když jste v kanceláři, a zastavit všechny virtuální počítače v rámci předplatného, když ponecháte práci v večer na základě **ststv2_vms_Scheduled_stopho** plánu.

Konfigurace aplikace logiky tak, aby jenom spouštěla virtuální počítače, je podporovaná.

Pro každý scénář můžete provést cíl akce s jedním nebo více předplatnými, jednou nebo více skupinami prostředků a určit jeden nebo více virtuálních počítačů v seznamu pro zahrnutí nebo vyloučení. Nemůžete je zadat společně ve stejné aplikaci logiky.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a pak přejděte k **Logic Apps**.

1. V seznamu aplikací logiky na konfigurovat naplánované spuštění vyberte **ststv2_vms_Scheduled_start**. Pokud chcete nakonfigurovat naplánované zastavení, vyberte **ststv2_vms_Scheduled_stop**.

1. V levém podokně vyberte **Návrhář aplikace logiky** .

1. Po zobrazení návrháře aplikace logiky vyberte v podokně návrháře možnost **opakování** a nakonfigurujte plán aplikace logiky. Další informace o konkrétních možnostech opakování najdete v tématu [plánování opakované úlohy](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurace četnosti opakování pro aplikaci logiky":::

1. V podokně návrháře vyberte **funkce – zkuste** nakonfigurovat nastavení cíle. Pokud v textu žádosti chcete spravovat virtuální počítače napříč všemi skupinami prostředků v rámci předplatného, upravte text žádosti, jak je znázorněno v následujícím příkladu.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     }
    }
    ```

    V poli zadejte více předplatných `subscriptions` s každou hodnotou oddělenou čárkou, jako v následujícím příkladu.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Pokud chcete spravovat virtuální počítače pro konkrétní skupiny prostředků, upravte v textu žádosti text žádosti, jak je znázorněno v následujícím příkladu. Každá zadaná cesta k prostředku musí být oddělená čárkou. V případě potřeby můžete zadat jednu skupinu prostředků nebo více.

    Tento příklad také ukazuje vyloučení virtuálního počítače. Virtuální počítač můžete vyloučit zadáním cesty k prostředkům virtuálního počítače nebo zástupným znakem.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

    Tato akce bude provedena na všech virtuálních počítačích s výjimkou toho, že název virtuálního počítače začíná na AZ a BZ v obou předplatných.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [“Az*”,“Bz*”],
       "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
    
        ]
      }
    }
    ```

    Pokud v textu žádosti chcete spravovat konkrétní sadu virtuálních počítačů v rámci předplatného, upravte text žádosti, jak je znázorněno v následujícím příkladu. Každá zadaná cesta k prostředku musí být oddělená čárkou. V případě potřeby můžete zadat jeden virtuální počítač.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg3/providers/Microsoft.Compute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
          
        ]
    }
    ```

## <a name="sequenced-start-and-stop-scenario"></a>Scénář spuštění a zastavení sekvencování

V prostředí, které obsahuje dvě nebo více komponent na více Azure Resource Manager virtuálních počítačů v distribuované aplikační architektuře, je důležité zajistit, aby se v daném pořadí podporovalo pořadí, ve kterém jsou komponenty spouštěny a zastaveny.

1. V seznamu aplikací logiky nakonfigurujte sekvencované spuštění výběrem **ststv2_vms_Sequenced_start**. Pokud chcete konfigurovat sekvencované zastavení, vyberte **ststv2_vms_Sequenced_stop**.

1. V levém podokně vyberte **Návrhář aplikace logiky** .

1. Po zobrazení návrháře aplikace logiky vyberte v podokně návrháře možnost **opakování** a nakonfigurujte plán aplikace logiky. Další informace o konkrétních možnostech opakování najdete v tématu [plánování opakované úlohy](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurace četnosti opakování pro aplikaci logiky":::

1. V podokně návrháře vyberte **funkce – zkuste** nakonfigurovat nastavení cíle. Pokud v textu žádosti chcete spravovat virtuální počítače napříč všemi skupinami prostředků v rámci předplatného, upravte text žádosti, jak je znázorněno v následujícím příkladu.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/"
        ]
     },
       "Sequenced": true
    }
    ```

    V poli zadejte více předplatných `subscriptions` s každou hodnotou oddělenou čárkou, jako v následujícím příkladu.

    ```json
    "Subscriptions": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/"
        ]
    ```

    Pokud chcete spravovat virtuální počítače pro konkrétní skupiny prostředků, upravte v textu žádosti text žádosti, jak je znázorněno v následujícím příkladu. Každá zadaná cesta k prostředku musí být oddělená čárkou. V případě potřeby můžete zadat jednu skupinu prostředků.

    Tento příklad také ukazuje vyloučení virtuálního počítače podle cesty prostředku v porovnání s příkladem pro plánované spuštění/zastavení, které používaly zástupné znaky.

    ```json
    {
      "Action": "start",
      "EnableClassic": false,
      "RequestScopes": {
        "ResourceGroups": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/"
        ],
        "ExcludedVMLists": [
         "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      },
       "Sequenced": true
    }
    ```

    Pokud v textu žádosti chcete spravovat konkrétní sadu virtuálních počítačů v rámci předplatného, upravte text žádosti, jak je znázorněno v následujícím příkladu. Každá zadaná cesta k prostředku musí být oddělená čárkou. V případě potřeby můžete zadat jeden virtuální počítač.

    ```json
    {
      "Action": "start",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1",
          "/subscriptions/12345678-1234-5678-1234-123456781234/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm2",
          "/subscriptions/11111111-0000-1111-2222-444444444444/resourceGroups/rg2/providers/Microsoft.ClassicCompute/virtualMachines/vm30"
        ]
      },
       "Sequenced": true
    }
    ```

## <a name="auto-stop-scenario"></a>Scénář automatického zastavení

Spuštění/zastavení virtuálních počítačů v2 (Preview) může pomáhat spravovat náklady na provoz Azure Resource Manager a klasických virtuálních počítačů ve vašem předplatném Pomocí vyhodnocení počítačů, které se nepoužívají během období mimo špičku, například po hodinách, a automaticky je vypíná, pokud je využití procesoru menší než zadané procento.

Následující vlastnosti výstrahy metriky v přizpůsobení podpory textu žádosti:

- AutoStop_MetricName
- AutoStop_Condition
- AutoStop_Threshold
- AutoStop_Description
- AutoStop_Frequency
- AutoStop_Severity
- AutoStop_Threshold
- AutoStop_TimeAggregationOperator
- AutoStop_TimeWindow

Další informace o tom, jak Azure Monitor výstrahy metrik fungují a jak je nakonfigurovat, najdete [v tématu upozornění na metriky v Azure monitor](../../azure-monitor/alerts/alerts-metric-overview.md).

1. V seznamu aplikací logiky pro konfiguraci automatického zastavení vyberte **ststv2_vms_AutoStop**.

1. V levém podokně vyberte **Návrhář aplikace logiky** .

1. Po zobrazení návrháře aplikace logiky vyberte v podokně návrháře možnost **opakování** a nakonfigurujte plán aplikace logiky. Další informace o konkrétních možnostech opakování najdete v tématu [plánování opakované úlohy](../../connectors/connectors-native-recurrence.md#add-the-recurrence-trigger).

    :::image type="content" source="media/deploy/schedule-recurrence-property.png" alt-text="Konfigurace četnosti opakování pro aplikaci logiky":::

1. V podokně návrháře vyberte **funkce – zkuste** nakonfigurovat nastavení cíle. Pokud v textu žádosti chcete spravovat virtuální počítače napříč všemi skupinami prostředků v rámci předplatného, upravte text žádosti, jak je znázorněno v následujícím příkladu.

    ```json
    {
      "Action": "stop",
      "EnableClassic": false,    
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "RequestScopes":{        
        "Subscriptions":[
            "/subscriptions/12345678-1111-2222-3333-1234567891234/",
            "/subscriptions/12345678-2222-4444-5555-1234567891234/"
        ],
        "ExcludedVMLists":[]
      }        
    }
    ```

    Pokud chcete spravovat virtuální počítače pro konkrétní skupiny prostředků, upravte v textu žádosti text žádosti, jak je znázorněno v následujícím příkladu. Každá zadaná cesta k prostředku musí být oddělená čárkou. V případě potřeby můžete zadat jednu skupinu prostředků.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "ResourceGroups": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroupsvmrg2/",
          "/subscriptions/12345678-2222-4444-5555-1234567891234/resourceGroups/VMHostingRG/"
          ]
      }
    }
    ```

    Pokud v textu žádosti chcete spravovat konkrétní sadu virtuálních počítačů v rámci předplatného, upravte text žádosti, jak je znázorněno v následujícím příkladu. Každá zadaná cesta k prostředku musí být oddělená čárkou. V případě potřeby můžete zadat jeden virtuální počítač.

    ```json
    {
      "Action": "stop",
      "AutoStop_Condition": "LessThan",
      "AutoStop_Description": "Alert to stop the VM if the CPU % exceed the threshold",
      "AutoStop_Frequency": "00:05:00",
      "AutoStop_MetricName": "Percentage CPU",
      "AutoStop_Severity": "2",
      "AutoStop_Threshold": "5",
      "AutoStop_TimeAggregationOperator": "Average",
      "AutoStop_TimeWindow": "06:00:00",
      "EnableClassic": true,
      "RequestScopes": {
        "ExcludedVMLists": [],
        "VMLists": [
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/rg3/providers/Microsoft.ClassicCompute/virtualMachines/Clasyvm11",
          "/subscriptions/12345678-1111-2222-3333-1234567891234/resourceGroups/vmrg1/providers/Microsoft.Compute/virtualMachines/vm1"
        ]
      }
    }
    ```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak monitorovat stav virtuálních počítačů Azure spravovaných funkcí spustit nebo zastavit virtuální počítače v2 (Preview) a provádět další úlohy správy, najdete v článku o [správě virtuálních počítačů spustit/zastavit](manage.md) .