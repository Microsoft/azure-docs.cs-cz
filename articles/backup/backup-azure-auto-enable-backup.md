---
title: Automatické povolení zálohování při vytváření virtuálních počítačů s využitím Azure Policy
description: Článek popisující, jak použít Azure Policy k automatickému povolení zálohování pro všechny virtuální počítače vytvořené v daném oboru
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 1d423371788f87155328e8f5c9334e47349a68e8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "77584264"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Automatické povolení zálohování při vytváření virtuálních počítačů s využitím Azure Policy

Jednou z klíčových zodpovědností správce zálohování nebo dodržování předpisů v organizaci je zajistit, aby všechny důležité podnikové počítače byly zálohovány s odpovídajícím uchováváním.

V současné době Azure Backup poskytuje předdefinované zásady (pomocí Azure Policy), které se dají přiřadit ke **všem virtuálním počítačům Azure v zadaném umístění v rámci předplatného nebo skupiny prostředků**. Když se tato zásada přiřadí k danému oboru, všechny nové virtuální počítače vytvořené v tomto oboru se automaticky nakonfigurují pro zálohování do **existujícího trezoru ve stejném umístění a předplatném**. Uživatel může zadat trezor a zásady uchovávání, ke kterým by se měly připojené virtuální počítače přidružit.

## <a name="supported-scenarios"></a>Podporované scénáře

* Předdefinované zásady se aktuálně podporují jenom pro virtuální počítače Azure. Uživatelé musí dbát na to, aby zásady uchovávání informací zadané během přiřazování byly zásadami uchovávání virtuálních počítačů. Pokud chcete zobrazit všechny SKU virtuálních počítačů podporované touto zásadou, přečtěte si [Tento](https://docs.microsoft.com/azure/backup/backup-azure-policy-supported-skus) dokument.

* Tato zásada se dá přiřadit k jednomu umístění a předplatnému v jednom okamžiku. Pokud chcete povolit zálohování virtuálních počítačů napříč umístěními a odběry, je potřeba vytvořit víc instancí přiřazení zásad, jednu pro každou kombinaci umístění a předplatného.

* Zadaný trezor a virtuální počítače, které jsou nakonfigurované pro zálohování, můžou být v různých skupinách prostředků.

* Obor skupiny pro správu se momentálně nepodporuje.

* Integrovaná zásada není v současnosti k dispozici v národních cloudech.

## <a name="using-the-built-in-policy"></a>Používání předdefinované zásady

Chcete-li přiřadit zásadu k požadovanému oboru, postupujte podle následujících kroků:

1. Přihlaste se k webu Azure Portal a přejděte na řídicí panel **zásad** .
2. V nabídce vlevo vyberte **definice** a získejte seznam všech předdefinovaných zásad napříč prostředky Azure.
3. Vyfiltrujte seznam pro **kategorii = zálohování**. Zobrazí se seznam filtrovaný podle jedné zásady s názvem konfigurace zálohování na virtuálních počítačích umístění do existujícího centrálního trezoru ve stejném umístění.
![Řídicí panel zásad](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Klikněte na název zásady. Budete přesměrováni na podrobnou definici této zásady.
![Okno Definice zásad](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. Klikněte na tlačítko **přiřadit** v horní části okna. Tím vás přesměruje na okno **přiřadit zásadu** .
6. V části **základy**klikněte na tři tečky vedle pole **obor** . Otevře se okno správného kontextu, kde můžete vybrat předplatné, na které se má zásada použít. Volitelně můžete také vybrat skupinu prostředků, aby se zásady používaly jenom pro virtuální počítače v určité skupině prostředků.
![Základy přiřazení zásad](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Na kartě **parametry** zvolte umístění z rozevíracího seznamu a vyberte trezor a zásady zálohování, ke kterým musí být přidružené virtuální počítače v daném oboru.
![Parametry přiřazení zásad](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Ujistěte se, že je **efekt** nastavený na deployIfNotExists.
9. Přejděte na **zkontrolovat + vytvořit** a klikněte na **vytvořit**.

> [!NOTE]
>
> Azure Policy můžete použít taky na stávajících virtuálních počítačích, a to pomocí [nápravy](https://docs.microsoft.com/azure/governance/policy/how-to/remediate-resources).

> [!NOTE]
>
> Doporučuje se, aby se tato zásada nepřiřazoval více než 200 virtuálních počítačů najednou. Pokud je zásada přiřazená k více než 200 virtuálním počítačům, může to vést k tomu, že zálohování bude vyvoláno několik hodin později než podle plánu.

## <a name="next-steps"></a>Další kroky

[Další informace o Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)
