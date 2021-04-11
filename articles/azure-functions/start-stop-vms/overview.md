---
title: Přehled spuštění/zastavení virtuálních počítačů v2 (Preview)
description: Tento článek popisuje dvě verze funkce Spustit/zastavit virtuální počítače (Preview), které spustí nebo zastaví Azure Resource Manager a klasické virtuální počítače podle plánu.
ms.topic: conceptual
ms.service: azure-functions
ms.subservice: ''
ms.date: 03/29/2021
ms.openlocfilehash: aac50f085d3ad63fb0183c19cf7d9aaa679e3d04
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111706"
---
# <a name="startstop-vms-v2-preview-overview"></a>Přehled spuštění/zastavení virtuálních počítačů v2 (Preview)

Funkce Spustit/zastavit virtuální počítače v2 (Preview) spustí nebo zastaví virtuální počítače Azure ve více předplatných. Spustí nebo zastaví virtuální počítače Azure na uživatelem definovaných plánech, poskytuje přehledy prostřednictvím [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)a odesílá volitelná oznámení pomocí [skupin akcí](../../azure-monitor/alerts/action-groups.md). Tato funkce může ve většině scénářů spravovat Azure Resource Manager virtuální počítače i klasické virtuální počítače.

Tato nová verze spuštění/zastavení virtuálních počítačů v2 (Preview) poskytuje pro zákazníky, kteří chtějí optimalizovat náklady na virtuální počítače, centralizovanou možnost automatizace s nízkými náklady. Nabízí všechny stejné funkce jako [původní verze](../../automation/automation-solution-vm-management.md) , které jsou k dispozici v Azure Automation, ale je navržena tak, aby využila výhod novější technologie v Azure.

## <a name="overview"></a>Přehled

Je přepracováno spouštění/zastavování virtuálních počítačů v2 (Preview) a nezávisí na Azure Automation ani Azure Monitor protokolech, podle požadavků [předchozí verze](../../automation/automation-solution-vm-management.md). Tato verze spoléhá na [Azure Functions](../../azure-functions/functions-overview.md) pro zpracování spuštění a zastavení spuštění virtuálního počítače.

Pro tuto Azure Functions aplikaci se vytvoří spravovaná identita ve službě Azure Active Directory (Azure AD) a umožňuje spustit nebo zastavit virtuální počítače v2 (Preview) a získat tak snadný přístup k dalším prostředkům chráněným službou Azure AD, jako jsou například aplikace logiky a virtuální počítače Azure. Další informace o spravovaných identitách v Azure AD najdete v tématu [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md).

Funkce koncového bodu HTTP triggeru je vytvořena tak, aby podporovala scénáře plánování a sekvence, které jsou součástí funkce, jak je znázorněno v následující tabulce.

|Name |Trigger |Description |
|-----|--------|------------|
|AlertAvailabilityTest |Časovač |Tato funkce provádí test dostupnosti, aby bylo zajištěno, že je primární funkce **AutoStopVM** vždy k dispozici.|
|Autostop |HTTP |Tato funkce podporuje scénář **autostop** , což je funkce vstupního bodu, která je volána z aplikace logiky.|
|AutoStopAvailabilityTest |Časovač |Tato funkce provede test dostupnosti, aby bylo zajištěno, že je primární funkce automaticky **zastavena** vždy k dispozici.|
|AutoStopVM |HTTP |Tato funkce se automaticky aktivuje výstrahou virtuálního počítače, když je podmínka výstrahy pravdivá.|
|CreateAutoStopAlertExecutor |Fronta |Tato funkce získá informace o datové části z funkce **autostop** pro vytvoření výstrahy na virtuálním počítači.|
|Plánované |HTTP |Tato funkce je určena pro naplánovaný i sekvenční scénář (odlišený podle schématu datové části). Jedná se o funkci vstupního bodu volanou z aplikace logiky a převezme datovou část ke zpracování operace spuštění nebo zastavení virtuálního počítače. |
|ScheduledAvailabilityTest |Časovač |Tato funkce provede test dostupnosti, aby bylo zajištěno, že je **naplánovaná** primární funkce vždy k dispozici.|
|VirtualMachineRequestExecutor |Fronta |Tato funkce provede skutečnou operaci spuštění a zastavení virtuálního počítače.|
|VirtualMachineRequestOrchestrator |Fronta |Tato funkce získá informace o datové části z **naplánované** funkce a orchestruje požadavky na spuštění a zastavení virtuálního počítače.|

Například funkce aktivačního triggeru **http se** používá ke zpracování scénářů plánování a sekvence. Podobně automatické **zastavení** funkce triggeru http zpracovává scénář automatického zastavení.

Pro podporu této funkce jsou vyžadovány funkce triggeru založené na frontách. Všechny triggery založené na časovači se používají k provedení testu dostupnosti a ke sledování stavu systému.

 [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) se používá ke konfiguraci a správě plánů spustit a zastavit pro virtuální počítač voláním funkce s použitím datové části JSON. Ve výchozím nastavení vytvoří během počátečního nasazení celkem pět Logic Apps následujících scénářů:

- Akce plánovaného spuštění a zastavení jsou založené na plánu, který zadáte pro Azure Resource Manager a klasické virtuální počítače. **ststv2_vms_Scheduled_start** a **ststv2_vms_Scheduled_stop** konfiguraci naplánovaného spuštění a zastavení.

- Akce sekvencované-spuštění a zastavení jsou založené na plánu, který cílí na virtuální počítače s předem definovanými značkami Sequence. Podporovány jsou pouze dvě pojmenované značky – **sequencestart** a **sequencestop**. **ststv2_vms_Sequenced_start** a **ststv2_vms_Sequenced_stop** konfiguraci sekvencované spuštění a zastavení.

    > [!NOTE]
    > Tento scénář podporuje pouze Azure Resource Manager virtuálních počítačů.

- Autostop – Tato funkce se používá k provedení akce zastavení u Azure Resource Manager i klasických virtuálních počítačů na základě jejího využití procesoru. Může se také jednat o naplánované provedení *Akce*, které vytvoří výstrahy na virtuálních počítačích a na základě této podmínky se aktivuje výstraha k provedení akce zastavení. **ststv2_vms_AutoStop** nakonfiguruje funkci automatického zastavení.

Každá akce spustit/zastavit podporuje přiřazení jednoho nebo více předplatných, skupin prostředků nebo seznamu virtuálních počítačů.

Účet Azure Storage, který je vyžadován funkcemi, je použit také v nabídce spustit nebo zastavit virtuální počítače v2 (Preview) pro dva účely:

   - Pomocí služby Azure Table Storage ukládá metadata operace provedení (tj. akci Spustit/zastavit virtuální počítač).

   - Používá Azure Queue Storage k podpoře Azure Functions triggerů založených na frontách.

Všechna data telemetrie, která jsou protokoly trasování z provádění aplikace Functions, se odešlou do vaší připojené instance Application Insights. Data telemetrie uložená v Application Insights můžete zobrazit ze sady předdefinovaných vizualizací prezentovaných ve sdíleném [řídicím panelu Azure](../../azure-portal/azure-portal-dashboards.md).

:::image type="content" source="media/overview/shared-dashboard-sml.png" alt-text="Řídicí panel pro spuštění/zastavení sdíleného stavu virtuálních počítačů":::

E-mailová oznámení se odesílají také v důsledku akcí prováděných na virtuálních počítačích.

## <a name="new-releases"></a>Nové verze

Po vydání nové verze nástroje spustit nebo zastavit virtuální počítače v2 (Preview) se vaše instance automaticky aktualizuje bez nutnosti ručního opětovného nasazení.

## <a name="supported-scoping-options"></a>Podporované možnosti oboru

### <a name="subscription"></a>Předplatné

Pokud potřebujete provést akci spustit a zastavit u všech virtuálních počítačů v rámci celého předplatného, můžete použít rozsah předplatného. Pokud to bude nutné, můžete v případě potřeby vybrat více předplatných.  

Můžete také zadat seznam virtuálních počítačů, které se mají vyloučit, a bude je ignorovat z akce. Můžete také použít zástupné znaky a zadat všechny názvy, které mohou být současně ignorovány.

### <a name="resource-group"></a>Skupina prostředků

Pokud potřebujete provést akci spuštění a zastavení na všech virtuálních počítačích zadáním jednoho nebo více názvů skupin prostředků a v rámci jednoho nebo více předplatných, můžete použít obor na skupinu prostředků.

Můžete také zadat seznam virtuálních počítačů, které se mají vyloučit, a bude je ignorovat z akce. Můžete také použít zástupné znaky a zadat všechny názvy, které mohou být současně ignorovány.

### <a name="vmlist"></a>VMList

Zadání seznamu virtuálních počítačů lze použít v případě, že potřebujete provést akci spuštění a zastavení na konkrétní sadě virtuálních počítačů a v rámci více předplatných. Tato možnost nepodporuje určení seznamu virtuálních počítačů, které se mají vyloučit.

## <a name="prerequisites"></a>Požadavky

- Musíte mít účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/).

- Vašemu účtu bylo udělené oprávnění [přispěvatele](../../role-based-access-control/built-in-roles.md#contributor) v předplatném.

- Spuštění/zastavení virtuálních počítačů v2 (Preview) je k dispozici ve všech globálních oblastech Azure, které jsou uvedeny v seznamu [produkty dostupné v oblasti](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=functions) pro Azure Functions. V případě Azure Governmentho cloudu je k dispozici pouze v oblasti) – Virginia pro státní správu USA.

## <a name="next-steps"></a>Další kroky

Pokud chcete tuto funkci nasadit, přečtěte si téma [nasazení virtuálních počítačů spustit/zastavit](deploy.md) (Preview).