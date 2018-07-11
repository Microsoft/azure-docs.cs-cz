---
title: Automatická aktualizace služby Mobility zotavení po havárii Azure do Azure | Dokumentace Microsoftu
description: Poskytuje přehled automatické aktualizace služby Mobility, při replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: rajanaki
ms.openlocfilehash: 81dbb61d696da84febc89563f946581315fdf527
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921434"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatická aktualizace služby Mobility v replikaci z Azure do Azure

Azure Site Recovery obsahuje měsíční vydávání verzí, se přidají vylepšení stávajících funkcí nebo nové balíčky, kde jsou vyřešeny známé problémy, pokud existuje. To znamenalo, že zůstat aktuální ve službě, musíte naplánovat pro nasazení těchto oprav, každý měsíc. Pokud se chcete vyhnout přes hlavičky přidružené k upgradu, uživatelé místo toho zvolit, aby ke správě aktualizací komponent Site Recovery. Jak [referenční dokumentace k architektuře](azure-to-azure-architecture.md) pro zotavení po havárii Azure do Azure, se nainstaluje služba Mobility na všechny virtuální počítače Azure u kterých je povolená replikace při replikaci virtuálních počítačů z jedné Azure oblasti do jiného. Jakmile povolíte automatické aktualizace, rozšíření služby Mobility aktualizuje s každou novou verzi. Tento dokument podrobně popisuje následující:

- Jak funguje automatické aktualizace?
- Povolit automatické aktualizace
- Běžné problémy a řešení potíží
 
## <a name="how-does-automatic-update-work"></a>Jak funguje automatické aktualizace

Jakmile povolíte Site Recovery provádět správu aktualizací, globální runbook (což je používané službami Azure) se nasazuje přes účet automation, který se vytvoří ve stejném předplatném jako trezor. Jeden účet automation se používá pro konkrétní trezor. Sada runbook kontroluje u každého virtuálního počítače v trezoru, pro které se automaticky aktualizuje zapnuté a zahájí upgrade rozšíření služby Mobility, pokud je dostupná novější verze. Výchozí plán pro runbook recurrs každý den v 12:00 podle časového pásma geograficky replikovaný virtuální počítač. Plán sad runbook můžete také upravit přes účet automation tímto uživatelem, v případě potřeby. 

> [!NOTE]
> Povolení automatické aktualizace nevyžaduje restartování virtuálních počítačů Azure a nemá vliv na probíhající replikaci.

> [!NOTE]
> Účtování úloh používá účet služby automation je podle počtu běhu, které jsou k dispozici jako volných jednotek pro účet automation minut za měsíc a ve výchozím nastavení 500 minut úloh. Spuštění úlohy denní množství nepřekračuje z **několik sekund, přibližně minutu** a bude **do bezplatné kredity**.

ZAHRNUTÉ volné jednotky (měsíčně) ** ₹0.14 500 minut čas spuštění úlohy cena / min

## <a name="enable-automatic-updates"></a>Povolit automatické aktualizace

Můžete zvolit, aby Site Recovery ke správě aktualizací následujícími způsoby:-

- [Jako součást kroku povolení replikace](#as-part-of-the-enable-replication-step)
- [Přepnout rozšíření aktualizovat nastavení uvnitř tohoto trezoru](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>Jako součást kroku povolení replikace:

Když povolíte replikaci pro virtuální počítač buď počáteční [ze zobrazení virtuálního počítače](azure-to-azure-quickstart.md), nebo [z trezoru služby recovery services](azure-to-azure-how-to-enable-replication.md), zobrazí se možnost zvolit, aby buď Site Recovery za účelem Správa aktualizací pro rozšíření služby Site Recovery, nebo ručně spravovat stejný.

![Povolení replikace automatické aktualizace](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Přepnout rozšíření aktualizovat nastavení uvnitř tohoto trezoru

1. Uvnitř tohoto trezoru, přejděte na **spravovat**-> **infrastruktura Site Recovery**
2. V části **pro virtuální počítače Azure**-> **nastavení aktualizací rozšíření**, klikněte na přepínač, chcete-li zvolit, jestli chcete povolit *Azure Site Recovery ke správě aktualizací* nebo *spravovat ručně*. Klikněte na **Uložit**.

![Trezor přepnout autuo aktualizace](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Pokud zvolíte *povolit správu pomocí ASR*, toto nastavení platí pro všechny virtuální počítače v odpovídající úložiště.


> [!Note] 
> Obě možnosti vás upozorní na účtu automation, který se používá pro správu aktualizací. Chcete-li povolit tuto funkci poprvé za trezor, vytvoří se nový účet automation. Všechny následné povolení replikace ve stejném trezoru použije dříve vytvořený.

## <a name="common-issues--troubleshooting"></a>Běžné problémy a řešení potíží

Pokud se vyskytl problém s automatickými aktualizacemi, budete upozorněni na stejné v části Konfigurace problémy v řídicím panelu trezoru. 

V případě pokusu povolit automatické aktualizace a jeho selhání, najdete níže pro řešení potíží.

**Chyba**: Nemáte oprávnění k vytvoření účtu spustit v Azure jako (instanční objekt) a udělení role Přispěvatel tomuto instančnímu objektu služby. 
- Doporučená akce: Zajištění, aby přihlášený účet přiřazený "Přispěvatel" a zkuste operaci zopakovat. Odkazovat na [to](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) dokument pro další informace o přiřazování oprávnění.
 
Po automatické aktualizace jsou zapnuté, většinu problémů může být opraven službou Site Recovery a vyžaduje, abyste na "**opravit**' tlačítko.

![tlačítko Opravit](./media/azure-to-azure-autoupdate/repair.png)

V případě, že tlačítko Opravit není k dispozici, najdete v podokně nastavení rozšíření zobrazenou chybovou zprávu.

 - **Chyba**: The spustit jako účet nemá oprávnění pro přístup k prostředku služby recovery services.

    **Doporučená akce**: odstraňte a pak [znovu vytvořte účet Spustit jako](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account) nebo se ujistěte, že aplikace účtu Automation spustit jako pro Azure Active Directory má přístup k prostředku služby recovery services.

- **Chyba**: nebyl nalezen účet Spustit jako. Jeden z nich byl odstraněn nebo není vytvořená: aplikace Azure Active Directory, instanční objekt, Role, prostředek certifikátu služby Automation, asset připojení Automation – nebo kryptografický otisk není mezi certifikátem a připojením identický. 

    **Doporučená akce**: odstraňte a [znovu vytvořte účet Spustit jako](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account).
