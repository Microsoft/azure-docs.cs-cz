---
title: Automatické povolení zálohování při vytváření virtuálních počítačů s využitím Azure Policy
description: Článek popisující, jak použít Azure Policy k automatickému povolení zálohování pro všechny virtuální počítače vytvořené v daném oboru
ms.topic: conceptual
ms.date: 11/08/2019
ms.openlocfilehash: 7e8195d22f54f29b36549b966322623ed0987d72
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896863"
---
# <a name="auto-enable-backup-on-vm-creation-using-azure-policy"></a>Automatické povolení zálohování při vytváření virtuálních počítačů s využitím Azure Policy

Jednou z klíčových zodpovědností správce zálohování nebo dodržování předpisů v organizaci je zajistit, aby všechny důležité podnikové počítače byly zálohovány s odpovídajícím uchováváním.

V současné době Azure Backup poskytuje celou řadu předdefinovaných zásad (pomocí [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview)), které vám pomůžou automaticky zajistit, aby byly virtuální počítače Azure nakonfigurované pro zálohování. V závislosti na způsobu uspořádání vašich zálohovacích týmů a prostředků můžete použít kteroukoli z následujících zásad:

## <a name="policy-1---configure-backup-on-vms-without-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Zásady 1 – Konfigurace zálohování na virtuálních počítačích bez dané značky do stávajícího trezoru služby Recovery Services ve stejném umístění

Pokud má vaše organizace centrální zálohovací tým, který spravuje zálohy napříč týmy aplikace, můžete pomocí této zásady nakonfigurovat zálohu na existující trezor centrálního Recovery Services ve stejném předplatném a umístění jako virtuální počítače, které se řídí. Z rozsahu této zásady se můžete rozhodnout pro **vyloučení** virtuálních počítačů, které obsahují určitou značku.

## <a name="policy-2---preview-configure-backup-on-vms-with-a-given-tag-to-an-existing-recovery-services-vault-in-the-same-location"></a>Zásada 2 – [Preview] Konfigurace zálohování virtuálních počítačů s danou značkou na existující trezor služby Recovery Services ve stejném umístění
Tato zásada funguje stejně jako zásada 1 výše, přičemž jediným rozdílem je, že tyto zásady můžete použít k **zahrnutí** virtuálních počítačů, které obsahují určitou značku, v rozsahu této zásady. 

## <a name="policy-3---preview-configure-backup-on-vms-without-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Zásady 3 – [Preview] Konfigurace zálohování na virtuálních počítačích bez dané značky do nového trezoru služby Recovery Services s výchozími zásadami
Pokud organizujete aplikace ve vyhrazených skupinách prostředků a chcete je zálohovat pomocí stejného trezoru, tato zásada vám umožní tuto akci automaticky spravovat. Z rozsahu této zásady se můžete rozhodnout pro **vyloučení** virtuálních počítačů, které obsahují určitou značku.

## <a name="policy-4---preview-configure-backup-on-vms-with-a-given-tag-to-a-new-recovery-services-vault-with-a-default-policy"></a>Zásada 4-[Preview] Konfigurace zálohování na virtuálních počítačích s danou značkou do nového trezoru služby Recovery Services s výchozími zásadami
Tato zásada funguje stejně jako u zásad 3 výše. jediným rozdílem je, že tyto zásady můžete použít k **zahrnutí** virtuálních počítačů, které obsahují určitou značku, v rozsahu této zásady. 

Kromě výše uvedeného Azure Backup taky poskytuje zásady [pouze pro audit](https://docs.microsoft.com/azure/governance/policy/concepts/effects#audit) – **Azure Backup by mělo být povolené Virtual Machines**. Tato zásada určuje, které virtuální počítače nemají zapnutou zálohu, ale nekonfiguruje pro tyto virtuální počítače automaticky zálohy. To je užitečné, když hledáte jenom vyhodnocení celkového dodržování předpisů u virtuálních počítačů, ale nechcete, aby se okamžitě projevila akce.

## <a name="supported-scenarios"></a>Podporované scénáře

* Předdefinované zásady se aktuálně podporují jenom pro virtuální počítače Azure. Uživatelé musí dbát na to, aby zásady uchovávání informací zadané během přiřazování byly zásadami uchovávání virtuálních počítačů. Pokud chcete zobrazit všechny SKU virtuálních počítačů podporované touto zásadou, přečtěte si [Tento](./backup-azure-policy-supported-skus.md) dokument.

* Zásady 1 a 2 se dají přiřadit k jednomu umístění a předplatnému v jednom okamžiku. Pokud chcete povolit zálohování virtuálních počítačů napříč umístěními a odběry, je potřeba vytvořit víc instancí přiřazení zásad, jednu pro každou kombinaci umístění a předplatného.

* V případě zásad 1 a 2 není obor skupiny pro správu aktuálně podporován.

* U zásad 1 a 2 může zadaný trezor a virtuální počítače nakonfigurované pro zálohování být v různých skupinách prostředků.

* Zásady 1, 2, 3 a 4 nejsou v současnosti k dispozici v národních cloudech.

* Zásady 3 a 4 se dají přiřadit k jednomu předplatnému v čase (nebo skupině prostředků v rámci předplatného).

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="using-the-built-in-policies"></a>Použití předdefinovaných zásad

Níže uvedené kroky popisují kompletní proces přiřazování zásad 1: **Konfigurace zálohování na virtuálních počítačích bez dané značky pro existující trezor služby Recovery Services ve stejném umístění** pro daný obor. Podobné pokyny budou platit pro ostatní zásady. Po přiřazení se všechny nové virtuální počítače vytvořené v oboru automaticky nakonfigurují pro zálohování.

1. Přihlaste se k Azure Portal a přejděte na řídicí panel **zásad** .
2. V nabídce vlevo vyberte **definice** a získejte seznam všech předdefinovaných zásad napříč prostředky Azure.
3. Vyfiltrujte seznam pro **kategorii = zálohování** a vyberte zásadu s názvem konfigurovat zálohu na virtuálních počítačích umístění do existujícího centrálního trezoru ve stejném umístění.
![Řídicí panel zásad](./media/backup-azure-auto-enable-backup/policy-dashboard.png)
4. Vyberte název zásady. Budete přesměrováni na podrobnou definici této zásady.
![Podokno definice zásad](./media/backup-azure-auto-enable-backup/policy-definition-blade.png)
5. V horní části podokna vyberte tlačítko **přiřadit** . Tím vás přesměruje do podokna **zásady přiřazení** .
6. V části **základy** vyberte tři tečky vedle pole **obor** . Otevře se pravé podokno kontextu, kde můžete vybrat předplatné, na které se má zásada použít. Volitelně můžete také vybrat skupinu prostředků, aby se zásady používaly jenom pro virtuální počítače v určité skupině prostředků.
![Základy přiřazení zásad](./media/backup-azure-auto-enable-backup/policy-assignment-basics.png)
7. Na kartě **parametry** zvolte umístění z rozevíracího seznamu a vyberte trezor a zásady zálohování, ke kterým musí být přidružené virtuální počítače v daném oboru. Můžete také zvolit, že chcete zadat název značky a pole hodnot značek. Virtuální počítač, který obsahuje některou z zadaných hodnot pro danou značku, bude vyloučen z oboru přiřazení zásady.
![Parametry přiřazení zásad](./media/backup-azure-auto-enable-backup/policy-assignment-parameters.png)
8. Ujistěte se, že je **efekt** nastavený na deployIfNotExists.
9. Přejděte na **Revize + vytvořit** a vyberte **vytvořit**.

> [!NOTE]
>
> Azure Policy můžete použít taky na stávajících virtuálních počítačích, a to pomocí [nápravy](../governance/policy/how-to/remediate-resources.md).

> [!NOTE]
>
> Doporučuje se, aby se tyto zásady nepřiřazované více než 200 virtuálních počítačů najednou. Pokud je zásada přiřazená k více než 200 virtuálním počítačům, může dojít k tomu, že se zálohování aktivuje několik hodin později než podle plánu.

## <a name="next-steps"></a>Další kroky

[Další informace o Azure Policy](../governance/policy/overview.md)
