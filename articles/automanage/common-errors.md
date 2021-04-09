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
ms.openlocfilehash: 18165ce5f39b32fe1c5af28bc88e8e1bd0e9cb62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955546"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Řešení běžných chyb při připojování běžných možností automanage
Při autosprávě se nemusí na službu připojit počítač. Tento dokument vysvětluje, jak řešit potíže s nasazením, sdílí některé běžné důvody, proč nasazení můžou selhat, a popisuje možné další kroky při zmírnění rizik.

## <a name="troubleshooting-deployment-failures"></a>Řešení potíží se selháním nasazení
Při připojování počítače k autosprávě dojde k vytvoření Azure Resource Managerho nasazení. Pokud se registrace nepovede, může být užitečné požádat o nasazení a získat další podrobnosti o tom, proč se nezdařila. V informačním rámečku s podrobnostmi o selhání jsou odkazy na nasazení, které následují po obrázku.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Umožňuje autospravovat informační rámeček s podrobnostmi o selhání.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Zkontroluje nasazení skupiny prostředků, která obsahuje virtuální počítač, který selhal.
Informační rámeček selhání bude obsahovat odkaz na nasazení v rámci skupiny prostředků obsahující počítač, který selhal při registraci, a název předpony, pomocí kterého můžete filtrovat nasazení. Kliknutím na odkaz přejdete do okna nasazení, kde pak můžete vyfiltrovat nasazení, abyste viděli, jak se mají na váš počítač provádět automatické správy nasazení. Pokud provádíte nasazení napříč několika oblastmi, ujistěte se, že ve správné oblasti kliknete na nasazení.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Zkontroluje nasazení předplatného obsahujícího virtuální počítač, který selhal.
Pokud nevidíte žádné chyby v nasazení skupiny prostředků, pak se v dalším kroku podívejte na nasazení ve vašem předplatném, které obsahuje virtuální počítač, který selhal při registraci. Klikněte na odkaz **nasazení pro předplatné** v informačním rámečku selhání a vyfiltrujte nasazení pomocí filtru **automanage-DefaultResourceGroup** . K filtrování nasazení použijte název skupiny prostředků z okna selhání. Název nasazení bude zaregistrovaný s názvem oblasti. Pokud provádíte nasazení napříč několika oblastmi, ujistěte se, že ve správné oblasti kliknete na nasazení.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Kontrolovat nasazení v předplatném propojeném s Log Analytics pracovním prostorem
Pokud se v rámci skupiny prostředků nebo předplatného obsahujícího váš virtuální počítač nezobrazuje žádná neúspěšná nasazení, a pokud je váš virtuální počítač připojený k Log Analytics pracovnímu prostoru v jiném předplatném, přejděte k předplatnému, které je propojené s vaším pracovním prostorem Log Analytics a vyhledejte neúspěšná nasazení.

## <a name="common-deployment-errors"></a>Běžné chyby nasazení

Chyba |  Omezení rizik
:-----|:-------------|
Chyba při autosprávě účtu s nedostatečnými oprávněními | K tomu může dojít v případě, že jste nedávno přesunuli předplatné obsahující nový účet pro správu do nového tenanta. Postup, jak tento problém vyřešit, najdete [tady](./repair-automanage-account.md).
Neshodující se požadavky na mapování oblastí v pracovním prostoru | Automatickou správou se nepovedlo připojit počítač, ale pracovní prostor Log Analytics, ke kterému je počítač aktuálně propojený, není namapovaný na podporovanou oblast automatizace. Ujistěte se, že je váš stávající pracovní prostor Log Analytics a účet Automation umístěný v [podporovaném mapování oblasti](../automation/how-to/region-mappings.md).
"Přístup byl odepřen z důvodu přiřazení zamítnutí názvu systému, které vytvořila spravovaná aplikace" ". | Ve vašem prostředku se vytvořilo [denyAssignment](../role-based-access-control/deny-assignments.md) , které vám zabránilo v přístupu k vašemu prostředku. To může být způsobeno buď podrobným [plánem](../governance/blueprints/concepts/resource-locking.md) , nebo [spravovanou aplikací](../azure-resource-manager/managed-applications/overview.md).
"Informace o operačním systému: název = ' (null) ', ver = ' (null), stav agenta = ' Nepřipraveno '. | Ujistěte se, že používáte [minimální podporovanou verzi agenta](/troubleshoot/azure/virtual-machines/support-extensions-agent-version), že je spuštěný agent[(Linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) a [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) a že je agent aktuální ([Linux](../virtual-machines/extensions/update-linux-agent.md) a [Windows](../virtual-machines/extensions/agent-windows.md)).
"Virtuální počítač ohlásil selhání při zpracování rozšíření" IaaSAntimalware ". | Ujistěte se, že na vašem VIRTUÁLNÍm počítači už není nainstalovaná jiná antimalwarová nebo antiantivirová nabídka. Pokud se tato chyba nezdařila, obraťte se na podporu.
Pracovní prostor ASC: automanage v současné době nepodporuje službu Log Analytics v daném _umístění_. | Ověřte, že se váš virtuální počítač nachází v [podporované oblasti](./automanage-virtual-machines.md#supported-regions).
Nasazení šablony se nepovedlo kvůli porušení zásad. Další informace najdete v podrobnostech. | K dispozici je zásada zabraňující autosprávě při připojování virtuálního počítače. Ověřte zásady, které se používají u svého předplatného nebo skupiny prostředků obsahující váš virtuální počítač, který chcete připojit k autosprávě.
"Přiřazení nebylo úspěšné; nejsou k dispozici žádné další informace. " | Otevřete prosím případ s podporou Microsoft Azure.

## <a name="next-steps"></a>Další kroky

* [Další informace o službě Azure automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Povolit automanage pro virtuální počítače v Azure Portal](quick-create-virtual-machines-portal.md)