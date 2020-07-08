---
title: Automatická aktualizace služby mobility v Azure Site Recovery
description: Přehled automatické aktualizace služby mobility při replikaci virtuálních počítačů Azure pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: rajanaki
ms.openlocfilehash: b2f5faea3df695500ea245d1dc71cb96a84c3643
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2020
ms.locfileid: "85985597"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatické aktualizace služby mobility v Azure do Azure – replikace

Azure Site Recovery používá tempo měsíčního vydání k řešení problémů a vylepšení existujících funkcí nebo přidávání nových. Chcete-li zůstat ve službě aktuální, je nutné naplánovat nasazení opravy každý měsíc. Abyste se vyhnuli režie spojené s jednotlivými upgrademi, můžete Site Recovery spravovat aktualizace součástí.

Jak je uvedeno v části [Architektura zotavení po havárii z Azure do Azure](azure-to-azure-architecture.md), Služba mobility je nainstalovaná na všech virtuálních počítačích Azure, které mají povolenou replikaci z jedné oblasti Azure do jiné. Pokud používáte automatické aktualizace, každé nové vydání aktualizuje rozšíření služby mobility.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Jak fungují automatické aktualizace

Když ke správě aktualizací použijete Site Recovery, nasadí globální Runbook (používaný službami Azure) prostřednictvím účtu Automation, který se vytvoří ve stejném předplatném jako trezor. Každý trezor používá jeden účet Automation. U každého virtuálního počítače v trezoru vyhledává sada Runbook aktivní automatické aktualizace. Pokud je k dispozici novější verze rozšíření služby mobility, nainstalují se aktualizace.

Výchozí plán Runbooku probíhá denně v 12:00 v časovém pásmu geograficky replikovaného virtuálního počítače. Můžete také změnit plán Runbooku prostřednictvím účtu Automation.

> [!NOTE]
> Počínaje [kumulativní aktualizací 35](site-recovery-whats-new.md#updates-march-2019)můžete zvolit existující účet Automation, který se bude používat pro aktualizace. Před kumulativní aktualizací 35 Site Recovery ve výchozím nastavení vytvořil účet Automation. Tuto možnost můžete vybrat jenom v případě, že pro virtuální počítač povolíte replikaci. Není k dispozici pro virtuální počítač, který už má povolenou replikaci. Nastavení, které vyberete, se vztahuje na všechny virtuální počítače Azure chráněné ve stejném trezoru.

Zapnutí automatických aktualizací nevyžaduje restartování virtuálních počítačů Azure nebo vlivu na probíhající replikaci.

Fakturace úlohy v účtu Automation vychází z počtu minut běhu úloh v měsíci. Provádění úlohy trvá několik sekund od každou minutu každý den a je zahrnuto jako volné jednotky. Ve výchozím nastavení jsou 500 minut zahrnuty jako volné jednotky pro účet Automation, jak je znázorněno v následující tabulce:

| Zahrnuté volné jednotky (každý měsíc) | Price |
|---|---|
| Běhové prostředí úlohy 500 minut | ₹ 0.14 za minutu

## <a name="enable-automatic-updates"></a>Povolit automatické aktualizace

Existuje několik způsobů, jak Site Recovery mohou spravovat aktualizace rozšíření:

- [Spravovat jako součást kroku Povolit replikaci](#manage-as-part-of-the-enable-replication-step)
- [Přepínání nastavení aktualizace rozšíření v rámci trezoru](#toggle-the-extension-update-settings-inside-the-vault)
- [Ruční správa aktualizací](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Spravovat jako součást kroku Povolit replikaci

Pokud povolíte replikaci pro virtuální počítač buď [od zobrazení virtuálního počítače](azure-to-azure-quickstart.md) , nebo [z trezoru služby Recovery Services](azure-to-azure-how-to-enable-replication.md), můžete povolit, aby Site Recovery spravovat aktualizace rozšíření Site Recovery, nebo ho spravovat ručně.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Nastavení rozšíření":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Přepínání nastavení aktualizace rozšíření v rámci trezoru

1. V trezoru Recovery Services klikněte na **Správa**  >  **Site Recovery infrastruktura**.
1. V **For Azure Virtual Machines**části  >  **Nastavení aktualizace rozšíření**Azure Virtual Machines  >  **umožňuje Site Recovery spravovat**, vyberte **zapnuto**.

   Pokud chcete rozšíření spravovat ručně, vyberte **vypnuto**.

1. Vyberte **Uložit**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Nastavení aktualizace rozšíření":::

> [!IMPORTANT]
> Když vyberete možnost **povoluje Site Recovery správě**, nastavení se použije na všechny virtuální počítače v trezoru.

> [!NOTE]
> Obě možnosti vás upozorní na účet Automation, který se používá ke správě aktualizací. Pokud tuto funkci používáte v trezoru poprvé, vytvoří se ve výchozím nastavení nový účet Automation. Alternativně můžete upravit nastavení a zvolit existující účet Automation. Všechny následné akce, které trvalo replikace ve stejném trezoru, budou používat dřív vytvořený účet Automation. V současné době rozevírací nabídka bude zobrazovat jenom účty Automation, které jsou ve stejné skupině prostředků jako trezor.

### <a name="manage-updates-manually"></a>Ruční správa aktualizací

1. Pokud jsou na virtuálních počítačích nainstalované nové aktualizace služby mobility, zobrazí se následující oznámení: **k dispozici je nová aktualizace agenta replikace Site Recovery. Kliknutím nainstalujete.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Okno replikované položky":::

1. Vyberte oznámení a otevřete stránku výběr virtuálního počítače.
1. Vyberte virtuální počítače, které chcete upgradovat, a pak vyberte **OK**. Služba mobility Update Service se spustí pro každý vybraný virtuální počítač.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Seznam virtuálních počítačů replikovaných položek":::

## <a name="common-issues-and-troubleshooting"></a>Běžné problémy a řešení potíží

Pokud dojde k potížím s automatickými aktualizacemi, zobrazí se v části **problémy s konfigurací** na řídicím panelu trezoru chybové oznámení.

Pokud nemůžete povolit automatické aktualizace, přečtěte si následující běžné chyby a doporučené akce:

- **Chyba**: nemáte oprávnění k vytvoření účtu spustit v Azure jako (instančního objektu) a udělení role přispěvateli objektu služby.

  **Doporučená akce**: Přesvědčte se, zda je přihlášený účet přiřazen jako Přispěvatel, a akci opakujte. Další informace o přiřazování oprávnění naleznete v části požadovaná oprávnění v tématu [Postupy: použití portálu k vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions).

  Chcete-li opravit většinu problémů po povolení automatických aktualizací, vyberte možnost **opravit**. Pokud není tlačítko opravit k dispozici, přečtěte si chybovou zprávu zobrazenou v podokně nastavení aktualizace rozšíření.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Tlačítko pro opravu služby Site Recovery v nastavení aktualizace rozšíření":::

- **Chyba**: účet Spustit jako nemá oprávnění pro přístup k prostředku služby Recovery Services.

  **Doporučená akce**: odstraňte a [znovu vytvořte účet Spustit jako](/azure/automation/automation-create-runas-account). Nebo se ujistěte, že aplikace Azure Active Directory účtu Spustit jako pro automatizaci má přístup k prostředku Recovery Services.

- **Chyba**: účet Spustit jako nebyl nalezen. Jedna z těchto položek byla odstraněna nebo nebyla vytvořena-Azure Active Directory aplikace, instanční objekt, role, Asset certifikátu Automation, Asset připojení Automation – nebo kryptografický otisk není totožný mezi certifikátem a připojením.

  **Doporučená akce**: odstraňte a [znovu vytvořte účet Spustit jako](/azure/automation/automation-create-runas-account).

- **Chyba**: platnost certifikátu spustit jako pro Azure, který používá účet Automation, brzy vyprší.

  Certifikát podepsaný svým držitelem, který se vytvoří pro účet Spustit jako, vyprší jeden rok od data vytvoření. Před vypršením platnosti ho můžete kdykoli obnovit. Pokud jste se zaregistrovali k e-mailovým oznámením, obdržíte také e-maily, když se z vaší strany vyžaduje akce. Tato chyba se zobrazí po dvou měsících před datem vypršení platnosti a v případě vypršení platnosti certifikátu se změní na kritickou chybu. Po vypršení platnosti certifikátu nebudou Automatické aktualizace funkční, dokud neobnovíte stejné.

  **Doporučená akce**: Chcete-li tento problém vyřešit, vyberte možnost **opravit** a pak **Obnovte certifikát**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="obnovit – certifikát":::

  > [!NOTE]
  > Po obnovení certifikátu aktualizujte stránku, aby se zobrazil aktuální stav.
