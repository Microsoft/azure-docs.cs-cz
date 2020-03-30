---
title: Automatické povolení zálohování při vytváření virtuálních počítačů s využitím Azure Policy
description: Článek popisující použití zásad Azure k automatickému povolení zálohování pro všechny virtuální počítače vytvořené v daném oboru
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77584264"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Automatické povolení zálohování při vytváření virtuálních počítačů s využitím Azure Policy

Jednou z klíčových povinností správce zálohování nebo dodržování předpisů v organizaci je zajistit, aby všechny počítače kritické pro podnikání byly zálohovány s příslušným uchováváním informací.

Azure Backup dnes poskytuje integrované zásady (pomocí zásad Azure), které se můžou přiřadit **ke všem virtuálním montovnám Azure v určeném umístění v rámci předplatného nebo skupiny prostředků.** Pokud je tato zásada přiřazena k danému oboru, všechny nové virtuální počítače vytvořené v tomto oboru jsou automaticky nakonfigurovány pro zálohování do **existujícího trezoru ve stejném umístění a předplatném**. Uživatel může určit trezor a zásady uchovávání informací, ke kterým by měly být přidruženy zálohované virtuální chody.

## <a name="supported-scenarios"></a>Podporované scénáře

* Předdefinované zásady se momentálně podporují jenom pro virtuální počítače Azure. Uživatelé musí dbát na to, aby zásady uchovávání informací zadané během přiřazení byly zásadami uchovávání informací virtuálního počítačů. V [tomto](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) dokumentu zobrazíte všechny virtuální sady virtuálních knih podporované touto zásadou.

* Zásady lze přiřadit k jednomu umístění a předplatné najednou. Chcete-li povolit zálohování pro virtuální počítače napříč umístěními a předplatnými, je třeba vytvořit více instancí přiřazení zásad, jednu pro každou kombinaci umístění a předplatného.

* Zadaný trezor a virtuální počítače nakonfigurované pro zálohování mohou být pod různými skupinami prostředků.

* Obor skupiny pro správu není aktuálně podporován.

* Vestavěná politika není v současné době k dispozici v národních cloudech.

## <a name="using-the-built-in-policy"></a>Použití předdefinovaných zásad

Chcete-li přiřadit zásady k požadovanému oboru, postupujte podle následujících kroků:

1. Přihlaste se na portál Azure Portal a přejděte na řídicí panel **zásad.**
2. V yberte **Definice** v levé nabídce, chcete-li získat seznam všech předdefinovaných zásad napříč prostředky Azure.
3. Filtrujte seznam pro **Category=Backup**. Seznam se odfiltruje do jedné zásady s názvem Konfigurace zálohování umístění na virtuálních počítačích do existujícího centrálního úložiště ve stejném umístění.
![Řídicí panel zásad](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Klikněte na název zásady. Budete přesměrováni na podrobnou definici těchto zásad.
![Okno definice zásad](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Klikněte na tlačítko **Přiřadit** v horní části čepele. Tím se přesměrujete na okno **Přiřadit zásady.**
6. V části **Základy**klikněte na tři tečky vedle pole **Obor.** Tím se otevře pravé okno kontextu, kde můžete vybrat předplatné pro zásady, které mají být použity na. Volitelně můžete také volitelně vybrat skupinu prostředků, aby se zásada použila jenom pro virtuální chody v určité skupině prostředků.
![Základy přiřazení zásad](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Na kartě **Parametry** zvolte umístění z rozevíracího seznamu a vyberte zásady úschovny a zálohování, ke kterým musí být virtuální počítače v oboru přidruženy.
![Parametry přiřazení zásad](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Ujistěte se, že **Effect** je nastavena na deployIfNotExists.
9. Přejděte na **tlačítko Revize+vytvořit** a klepněte na **tlačítko Vytvořit**.

> [!NOTE]
>
> Zásady Azure se taky můžou použít na stávajících virtuálních počítačích pomocí [nápravy](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> Doporučujese, aby tato zásada nebyla přiřazena k více než 200 virtuálním mům najednou. Pokud je zásada přiřazena k více než 200 virtuálním počítačem, může dojít k aktivaci zálohy o několik hodin později, než je uvedeno v plánu.

## <a name="next-steps"></a>Další kroky

[Další informace o zásadách Azure](https://docs.microsoft.com/azure/governance/policy/overview)
