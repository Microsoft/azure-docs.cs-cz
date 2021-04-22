---
title: Řešení běžných chyb při připojování k běžným službám Azure automanage
description: Běžné chyby při připojování a jejich řešení
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 6ee0164dd8243d30cf691350352757f2503e34c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862970"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Řešení běžných chyb při připojování běžných možností automanage
Při autosprávě se nemusí na službu připojit počítač. Tento dokument vysvětluje, jak řešit potíže s nasazením, sdílí některé běžné důvody, proč nasazení můžou selhat, a popisuje možné další kroky při zmírnění rizik.

## <a name="troubleshooting-deployment-failures"></a>Řešení potíží se selháním nasazení
Při připojování počítače k autosprávě dojde k vytvoření Azure Resource Managerho nasazení. Další informace najdete v nasazení, kde najdete další podrobnosti o tom, proč se selhání nezdařila. V informačním rámečku s podrobnostmi o selhání jsou odkazy na nasazení, které následují po obrázku.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Umožňuje autospravovat informační rámeček s podrobnostmi o selhání.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Zkontroluje nasazení skupiny prostředků, která obsahuje virtuální počítač, který selhal.
Informační rámeček selhání bude obsahovat odkaz na nasazení ve skupině prostředků obsahující počítač, který selhal při připojování. Informační rámeček bude obsahovat také název předpony, který můžete použít k filtrování nasazení pomocí. Kliknutím na odkaz nasazení přejdete do okna nasazení, kde pak můžete vyfiltrovat nasazení, abyste viděli, jak se mají na váš počítač provádět automatické správy nasazení. Pokud provádíte nasazení napříč několika oblastmi, ujistěte se, že ve správné oblasti kliknete na nasazení.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Zkontroluje nasazení předplatného obsahujícího virtuální počítač, který selhal.
Pokud nevidíte žádné chyby v nasazení skupiny prostředků, pak se v dalším kroku podívejte na nasazení ve vašem předplatném, které obsahuje virtuální počítač, který selhal při registraci. Klikněte na odkaz **nasazení pro předplatné** v informačním rámečku selhání a vyfiltrujte nasazení pomocí filtru **automanage-DefaultResourceGroup** . K filtrování nasazení použijte název skupiny prostředků z okna selhání. Název nasazení bude zaregistrovaný s názvem oblasti. Pokud provádíte nasazení napříč několika oblastmi, ujistěte se, že ve správné oblasti kliknete na nasazení.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Kontrolovat nasazení v předplatném propojeném s Log Analytics pracovním prostorem
Pokud se v rámci skupiny prostředků nebo předplatného obsahujícího váš virtuální počítač nezobrazuje žádná neúspěšná nasazení, a pokud je váš virtuální počítač připojený k Log Analytics pracovnímu prostoru v jiném předplatném, přejděte k předplatnému, které je propojené s vaším pracovním prostorem Log Analytics a vyhledejte neúspěšná nasazení.

## <a name="common-deployment-errors"></a>Běžné chyby nasazení

Chyba |  Omezení rizik
:-----|:-------------|
Chyba při autosprávě účtu s nedostatečnými oprávněními | K této chybě může dojít, pokud jste nedávno přesunuli předplatné obsahující nový účet pro správu do nového tenanta. Postup řešení této chyby najdete [tady](./repair-automanage-account.md).
Neshodující se požadavky na mapování oblastí v pracovním prostoru | Automatickou správou se nepovedlo připojit počítač, protože Log Analytics pracovní prostor, ke kterému je počítač aktuálně propojený, není namapovaný na podporovanou oblast automatizace. Ujistěte se, že je váš stávající pracovní prostor Log Analytics a účet Automation umístěný v [podporovaném mapování oblasti](../automation/how-to/region-mappings.md).
"Přístup byl odepřen z důvodu přiřazení zamítnutí názvu systému, které vytvořila spravovaná aplikace" ". | Ve vašem prostředku se vytvořilo [denyAssignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) , což brání tomu, aby se pro přístup k vašemu prostředku spravovala. Tato denyAssignment mohla být vytvořena [buď pomocí](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) podrobného plánu, nebo [spravované aplikace](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview).
"Informace o operačním systému: název = ' (null) ', ver = ' (null), stav agenta = ' Nepřipraveno '. | Ujistěte se, že používáte [minimální podporovanou verzi agenta](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version), že je spuštěný agent[(Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) a [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) a že je agent aktuální ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) a [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
"Nepovedlo se určit operační systém pro název operačního systému virtuálního počítače:, ver. Zkontrolujte prosím, jestli je spuštěný agent virtuálního počítače. aktuální stav je připravený. | Ujistěte se, že používáte [minimální podporovanou verzi agenta](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version), že je spuštěný agent[(Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) a [Windows](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) a že je agent aktuální ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) a [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)).
"Virtuální počítač ohlásil selhání při zpracování rozšíření" IaaSAntimalware ". | Ujistěte se, že na vašem VIRTUÁLNÍm počítači už není nainstalovaná jiná antimalwarová nebo antiantivirová nabídka. Pokud se tato chyba nezdařila, obraťte se na podporu.
Pracovní prostor ASC: automanage v současné době nepodporuje službu Log Analytics v daném _umístění_. | Ověřte, že se váš virtuální počítač nachází v [podporované oblasti](./automanage-virtual-machines.md#supported-regions).
Nasazení šablony se nepovedlo kvůli porušení zásad. Další informace najdete v podrobnostech. | K dispozici je zásada zabraňující autosprávě při připojování virtuálního počítače. Ověřte zásady, které se používají u svého předplatného nebo skupiny prostředků obsahující váš virtuální počítač, který chcete připojit k autosprávě.
"Přiřazení nebylo úspěšné; nejsou k dispozici žádné další informace. " | Otevřete prosím případ s podporou Microsoft Azure.

## <a name="next-steps"></a>Další kroky

* [Další informace o službě Azure automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Povolit automanage pro virtuální počítače v Azure Portal](quick-create-virtual-machines-portal.md)