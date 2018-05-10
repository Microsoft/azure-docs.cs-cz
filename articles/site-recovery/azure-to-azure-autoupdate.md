---
title: Automatické aktualizace služby Mobility v zotavení po havárii Azure do Azure | Microsoft Docs
description: Obsahuje přehled automatické aktualizace služby Mobility, který se používá pro replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/02/2018
ms.author: rajanaki
ms.openlocfilehash: d9b653e4766746d2142a7e1040d6d60ec2aacc44
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="automatic-update-of-mobility-service-extension-in-azure-to-azure-replication"></a>Automatické aktualizace služby Mobility rozšíření Azure do Azure replikace

Azure Site Recovery má měsíční cadence verzi jsou přidány vylepšení stávajících funkcí nebo nové, kde jsou pevné známé problémy, pokud existuje. To by znamenalo, že zůstat aktuální službou, budete muset naplánovat nasazení těchto oprav měsíčně. Aby se zabránilo selhání hlavičky přidružené k upgradu, uživatelé mohou místo toho možnost obnovení lokality ke správě aktualizací součástí. Podle popisu v [referenční architektura](azure-to-azure-architecture.md) pro zotavení po havárii Azure do Azure, získá službu Mobility nainstalovat na všechny Azure virtuální počítače, pro které je povolená replikace při replikaci virtuálních počítačů z jedné Azure oblast na jiný. Jakmile povolíte automatických aktualizací, získá rozšíření služby Mobility aktualizováno každých novou verzi. Tento dokument podrobně popisuje následující:

- Jak funguje automatické aktualizace?
- Povolit automatické aktualizace
- Běžné problémy a řešení potíží
 
## <a name="how-does-automatic-update-work"></a>Jak funguje automatické aktualizace

Jakmile povolíte Site Recovery ke správě aktualizací, globální runbook (který je využíván službou Azure services) se nasazuje přes účet automation, který je vytvořen ve stejném předplatném jako trezor. Jeden účet automation se používá pro konkrétní trezoru. Sada runbook zkontroluje pro každý virtuální počítač v trezoru, pro kterou jsou automatické aktualizace zapnuté ON a zahájí upgrade rozšíření služby Mobility, pokud je k dispozici novější verze. Výchozí plán recurrs runbook každý den v 12:00 AM podle časového pásma geograficky replikovaného virtuálního počítače. Plán sad runbook můžete také upravit prostřednictvím účtu automation uživatelem, v případě potřeby. 

> [!NOTE]
> Povolení funkce Automatické aktualizace nevyžaduje restartování virtuální počítače Azure a nemá vliv na probíhající replikace.

## <a name="enable-automatic-updates"></a>Povolit automatické aktualizace

Můžete zvolit, aby Site Recovery ke správě aktualizací v následujících způsobů:-

- [Jako součást kroku povolení replikace](#as-part-of-the-enable-replication-step)
- [Přepnutí rozšíření aktualizovat nastavení v trezoru](#toggle-the-extension-update-settings-inside-the-vault)

### <a name="as-part-of-the-enable-replication-step"></a>Jako součást kroku povolení replikace:

Když povolíte replikaci pro virtuální počítač buď od [ze zobrazení virtuálního počítače](azure-to-azure-quickstart.md), nebo [z trezoru služeb zotavení](azure-to-azure-how-to-enable-replication.md), budete mít možnost zvolit, aby buď Site Recovery k Spravujte aktualizace pro rozšíření Site Recovery nebo spravovat stejný ručně.

![Povolení replikace automatickou aktualizaci](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Přepnutí rozšíření aktualizovat nastavení v trezoru

1. V úložišti, přejděte na **spravovat**-> **infrastruktura Site Recovery**
2. V části **Azure pro virtuální počítače**-> **nastavení aktualizace rozšíření**, klikněte na přepínač zvolit, zda chcete povolit *automatické obnovení systému ke správě aktualizací* nebo *spravovat ručně*. Klikněte na **Uložit**.

![Trezor přepnutí autuo aktualizace](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important] 
> Pokud vyberete *povolit automatické obnovení systému pro správu*, nastavení se použije pro všechny virtuální počítače v odpovídající úložiště.


> [!Note] 
> Obě možnosti budou upozorňovat na účtu automation, který se používá pro správu aktualizací. Chcete-li povolit tuto funkci poprvé v trezoru, vytvoří se nový účet automation. Všechny následné povolení replikace v ke stejnému trezoru použije dříve vytvořenou jeden.

## <a name="common-issues--troubleshooting"></a>Běžné problémy a řešení potíží

Pokud nastane problém s automatickými aktualizacemi, budete upozorněni na stejné v části Konfigurace problémy v řídícím panelu trezoru. 

V případě, že jste se pokusili povolit automatické aktualizace a se nezdařilo, najdete níže pro řešení potíží.

**Chyba**: Nemáte oprávnění k vytvoření účtu spustit v Azure jako (instanční objekt) a udělit objektu služby roli Přispěvatel. 
- Doporučená akce: Zkontrolujte, že je přihlášený účet přiřazené 'přispěvatele a operaci opakujte.
 
Po automatické aktualizace se zapnou ON, Většina problémů můžete zacelené službou Site Recovery a vyžaduje, abyste klikněte na '**Repair**' tlačítko.

![Oprava – tlačítko](./media/azure-to-azure-autoupdate/repair.png)

V případě, že není k dispozici tlačítko opravit, naleznete v části podokno nastavení rozšíření zobrazenou chybovou zprávu.

 - **Chyba**: Spustit jako účet nemá oprávnění pro přístup k prostředku služby obnovení.

    **Doporučená akce**: odstraňte a potom [znovu vytvořit účet Spustit jako](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account) nebo se přesvědčte, že aplikace účtu Automation spustit jako Azure Active Directory má přístup k prostředku služby obnovení.

- **Chyba**: nebyl nalezen účet Spustit jako. Buď jednu z těchto byla odstraněna nebo pokud nebyla vytvořena - aplikace Azure Active Directory, instančního objektu, Role, prostředek certifikátu automatizace, asset připojení Automation - nebo kryptografický otisk nejsou identické certifikát a připojení. 

    **Doporučená akce**: odstraňte a [potom je znovu vytvořit účet Spustit jako](https://docs.microsoft.com/en-us/azure/automation/automation-create-runas-account).
