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
ms.openlocfilehash: df5133ad4bb3155afdc9d43e595591d9cfda4ea0
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644438"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Řešení běžných chyb při připojování běžných možností automanage
Při autosprávě se nemusí na službu připojit počítač. Tento dokument vysvětluje, jak řešit potíže s nasazením, sdílí některé běžné důvody, proč nasazení můžou selhat, a popisuje možné další kroky při zmírnění rizik.

## <a name="troubleshooting-deployment-failures"></a>Řešení potíží se selháním nasazení
Při připojování počítače k autosprávě dojde k vytvoření Azure Resource Managerho nasazení. Pokud se registrace nepovede, může být užitečné požádat o nasazení a získat další podrobnosti o tom, proč se nezdařila. V informačním rámečku s podrobnostmi o selhání jsou odkazy na nasazení, které následují po obrázku.

:::image type="content" source="media\automanage-common-errors\failure-flyout.png" alt-text="Umožňuje autospravovat informační rámeček s podrobnostmi o selhání.":::

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
"Přiřazení nebylo úspěšné; nejsou k dispozici žádné další informace. " | Otevřete prosím případ s podporou Microsoft Azure.

## <a name="next-steps"></a>Další kroky

* [Další informace o službě Azure automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Povolit automanage pro virtuální počítače v Azure Portal](quick-create-virtual-machines-portal.md)