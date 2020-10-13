---
title: Jak povolit prostředek řešení Azure VMware
description: Přečtěte si, jak odeslat žádost o podporu pro povolení prostředku řešení Azure VMware. Ve svém stávajícím privátním cloudu řešení Azure VMware si také můžete vyžádat další uzly.
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: dc90a322b5592ca7f400a82deca65ea753711c27
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/12/2020
ms.locfileid: "91948727"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Jak povolit prostředek řešení Azure VMware
Přečtěte si, jak odeslat žádost o podporu pro povolení prostředku řešení Azure VMware. Ve svém stávajícím privátním cloudu řešení Azure VMware si také můžete vyžádat další uzly.

## <a name="eligibility-criteria"></a>Kritéria způsobilosti

* Budete potřebovat [Azure smlouva Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) s Microsoftem.
* Budete potřebovat účet Azure v předplatném Azure.


## <a name="enable-azure-vmware-solution-resource"></a>Povolit prostředek řešení Azure VMware
Před vytvořením prostředku řešení Azure VMware budete muset odeslat lístek podpory, abyste měli své uzly přidělené. Jakmile tým podpory obdrží vaši žádost, trvá vám až pět pracovních dní, aby vaši žádost zkontroloval a rozdělil vaše uzly. Pokud máte existující privátní cloud řešení Azure VMware a chcete přidělit více uzlů, Projděte si stejný postup.


1. V Azure Portal v části **pomoc a podpora**vytvořte **[novou žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support)** a zadejte pro lístek následující informace:
   - **Typ problému:** Odbornou
   - **Předplatné:** Vyberte své předplatné.
   - **Služba:** Všechny služby > řešení Azure VMware
   - **Prostředek:** Obecná otázka 
   - **Shrnutí:** Potřebná kapacita
   - **Typ problému:** Problémy se správou kapacity
   - **Podtyp problému:** Požadavek zákazníka na další kvótu/kapacitu hostitele

1. V **popisu** lístku podpory zadejte na kartě **Podrobnosti** :

   - KONCEPCE nebo produkce 
   - Název oblasti
   - Počet uzlů
   - Všechny další podrobnosti

   >[!NOTE]
   >Řešení Azure VMware doporučuje minimálně tři uzly, aby se vymíchoval váš privátní cloud a pro redundanci N + 1 uzlů. 

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** odešlete žádost.

   Může trvat až pět pracovních dnů, než se zástupce podpory potvrdí vaší žádosti.

   >[!IMPORTANT] 
   >Pokud už máte nějaké řešení Azure VMware a požadujete další uzly, je potřeba si uvědomit, že k alokaci uzlů potřebujeme pět pracovních dnů. 

1. Než budete moci zřídit své uzly, nezapomeňte zaregistrovat poskytovatele prostředků **Microsoft. AVS** v Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Další způsoby registrace poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).