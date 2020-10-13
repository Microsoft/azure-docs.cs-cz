---
title: Automatické povolení zálohování při vytváření virtuálních počítačů s využitím Azure Policy
description: Článek popisující, jak použít Azure Policy k automatickému povolení zálohování pro všechny virtuální počítače vytvořené v daném oboru
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: de4923000bc842203535e03727fd532c67a8f517
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88826068"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Automatické povolení zálohování při vytváření virtuálních počítačů s využitím Azure Policy

Jednou z klíčových zodpovědností správce zálohování nebo dodržování předpisů v organizaci je zajistit, aby všechny důležité podnikové počítače byly zálohovány s odpovídajícím uchováváním.

V současné době Azure Backup poskytuje předdefinované zásady (pomocí Azure Policy), které se dají přiřadit ke **všem virtuálním počítačům Azure v zadaném umístění v rámci předplatného nebo skupiny prostředků**. Když se tato zásada přiřadí k danému oboru, všechny nové virtuální počítače vytvořené v tomto oboru se automaticky nakonfigurují pro zálohování do **existujícího trezoru ve stejném umístění a předplatném**. Uživatel může zadat trezor a zásady uchovávání, ke kterým by měly být připojené záložní virtuální počítače.

## <a name="supported-scenarios"></a>Podporované scénáře

* Předdefinované zásady se aktuálně podporují jenom pro virtuální počítače Azure. Uživatelé musí dbát na to, aby zásady uchovávání informací zadané během přiřazování byly zásadami uchovávání virtuálních počítačů. Pokud chcete zobrazit všechny SKU virtuálních počítačů podporované touto zásadou, přečtěte si [Tento](./backup-azure-policy-supported-skus.md) dokument.

* Tato zásada se dá přiřadit k jednomu umístění a předplatnému v jednom okamžiku. Pokud chcete povolit zálohování virtuálních počítačů napříč umístěními a odběry, je potřeba vytvořit víc instancí přiřazení zásad, jednu pro každou kombinaci umístění a předplatného.

* Zadaný trezor a virtuální počítače, které jsou nakonfigurované pro zálohování, můžou být v různých skupinách prostředků.

* Obor skupiny pro správu se momentálně nepodporuje.

* Integrovaná zásada není v současnosti k dispozici v národních cloudech.

## <a name="using-the-built-in-policy"></a>Používání předdefinované zásady

Chcete-li přiřadit zásadu k požadovanému oboru, postupujte podle následujících kroků:

1. Přihlaste se k Azure Portal a přejděte na řídicí panel **zásad** .
1. V nabídce vlevo vyberte **definice** a získejte seznam všech předdefinovaných zásad napříč prostředky Azure.
1. Vyfiltrujte seznam pro **kategorii = zálohování**. Zobrazí se seznam filtrovaný podle jedné zásady s názvem konfigurace zálohování na virtuálních počítačích umístění do existujícího centrálního trezoru ve stejném umístění.
![Řídicí panel zásad](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
1. Vyberte název zásady. Budete přesměrováni na podrobnou definici této zásady.
![Podokno definice zásad](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
1. V horní části podokna vyberte tlačítko **přiřadit** . Tím vás přesměruje do podokna **zásady přiřazení** .
1. V části **základy**vyberte tři tečky vedle pole **obor** . Otevře se pravé podokno kontextu, kde můžete vybrat předplatné, na které se má zásada použít. Volitelně můžete také vybrat skupinu prostředků, aby se zásady používaly jenom pro virtuální počítače v určité skupině prostředků.
![Základy přiřazení zásad](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
1. Na kartě **parametry** zvolte umístění z rozevíracího seznamu a vyberte trezor a zásady zálohování, ke kterým musí být přidružené virtuální počítače v daném oboru.
![Parametry přiřazení zásad](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
1. Ujistěte se, že je **efekt** nastavený na deployIfNotExists.
1. Přejděte na **Revize + vytvořit** a vyberte **vytvořit**.

> [!NOTE]
>
> Azure Policy můžete použít taky na stávajících virtuálních počítačích, a to pomocí [nápravy](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Doporučuje se, aby se tyto zásady nepřiřazované více než 200 virtuálních počítačů najednou. Pokud je zásada přiřazená k více než 200 virtuálním počítačům, může dojít k tomu, že se zálohování aktivuje několik hodin později než podle plánu.

## <a name="next-steps"></a>Další kroky

[Další informace o Azure Policy](../governance/policy/overview.md)
