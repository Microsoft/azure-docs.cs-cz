---
title: Průvodce řízením chování při vypínání Windows ve službě Azure Lab Services | Dokumenty společnosti Microsoft
description: Postup automatického vypnutí nečinného virtuálního počítače se systémem Windows a odebrání příkazu vypnutí systému Windows.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 39ff4f42457451dfa4aae90b281d6b163c56b4cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522235"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Průvodce řízením chování při vypínání systému Windows

Azure Lab Services poskytuje několik ovládacích prvků nákladů, které zajišťují, že virtuální počítače (VM) Windows neběží neočekávaně:
 - [Nastavení plánu](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [Nastavení kvót pro uživatele](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [Povolení automatického vypnutí při odpojení](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

I s těmito řízení nákladů existují situace, kdy virtuální počítače systému Windows může neočekávaně nadále spustit; a v důsledku toho odečíst od studenta kvóty:

- **Okno RDP je ponecháno otevřené**
  
    Když se student připojí ke svému virtuálnímu virtuálnímu zařízení pomocí rdp, může nechtěně nechat okno RDP otevřené.  Dokud zůstane otevřené okno RDP, **automatické vypnutí při odpojení** se nikdy neprojeví, protože se aktivuje až po odpojení relace RDP.

- **Příkaz vypnutí systému Windows se používá k vypnutí virtuálního počítače**
  
    Student může použít příkaz vypnutí systému Windows nebo jiné mechanismy vypnutí poskytované v systému Windows k vypnutí virtuálního počítače namísto použití [tlačítka stop služby Azure Lab Services](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm).  Když k tomu dojde, z hlediska Azure Lab Services virtuální počítač se stále používá.
    
Chcete-li zabránit těmto situacím, tato příručka poskytuje kroky k automatickému vypnutí nečinného virtuálního počítače se systémem Windows a odebrání příkazu vypnutí systému Windows z nabídky **Start.**  

> [!NOTE]
> Virtuální počítače může také neočekávaně odečíst z kvóty při student spustí svůj virtuální počítače, ale nikdy ve skutečnosti se k němu připojí pomocí RDP.  Tato příručka aktuálně *neřeší* tento scénář.  Místo toho by studentům mělo být připomenuto, aby se okamžitě připojili ke svému virtuálnímu virtuálnímu zařízení pomocí rdp po jeho spuštění; nebo by měli zastavit virtuální hod.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>Automatické odpojení a vypnutí RDP pro nečinný virtuální počítač

Systém Windows poskytuje nastavení **místních zásad skupiny,** pomocí kterého můžete nastavit časový limit pro automatické odpojení relace protokolu RDP při jeho nečinnosti.  Relace je určena jako nečinná, pokud *není* zadán žádný vstup myši\klávesnice.  Všechny dlouhotrvající aktivity, které nezahrnují vstup myši\klávesnice způsobit, že virtuální jazyk v nečinnosti stavu.  To zahrnuje provádění dlouhého dotazu, streamování videa, kompilaci atd.  V závislosti na potřebách vaší třídy můžete nastavit časový limit nečinnosti tak, aby byl dostatečně dlouhý pro zpracování těchto typů aktivit.  V případě potřeby můžete například nastavit časový limit nečinnosti na 1 nebo více hodin.

Zde je zkušenost studenta při konfiguraci **limitu nečinnosti relace** v kombinaci s [**automatickým vypnutím při nastavení odpojení:**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)
 1. Student se připojí ke svému virtuálnímu virtuálnímu virtuálnímu virtuálnímu připojení systému Windows pomocí rdp.
 2. Když student nechá otevřené okno RDP a virtuální vír je nečinný pro **limit nečinnosti relace,** který jste zadali (například 5 minut), zobrazí se mu následující dialogové okno:

    ![Dialogové okno Vypršela doba nečinnosti](../media/how-to-windows-shutdown/idle-time-expired.png)

1. Pokud student *neklepne* na **tlačítko OK**, jeho relace RDP se automaticky odpojí po 2 minutách.
2. Po odpojit relace RDP, jakmile je dosaženo zadaného časového rámce pro **automatické vypnutí při odpojení** nastavení, virtuální počítač se automaticky vypne službou Azure Lab Services.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>Nastavení časového limitu relace nečinnosti rdp na virtuálním počítači šablony

Chcete-li nastavit časový limit nečinnosti relace RDP, můžete se připojit k virtuálnímu počítače šablony a spustit níže uvedený skript prostředí PowerShell.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 90

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
Nebo můžete postupovat podle těchto ručních kroků pomocí šablony virtuálního počítače:

1. Stiskněte klávesu Windows, zadejte **příkaz gpedit**a vyberte **možnost Upravit zásady skupiny (Ovládací panely).**

1. Přejděte na **položku Konfigurace počítače > šablony pro správu > součásti systému Windows > služby Vzdálená plocha > Čas> časová vzdálené plochy**.  

    ![Editor zásad místní skupiny](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. Klepněte pravým tlačítkem myši na příkaz **Nastavit časový limit pro aktivní, ale nečinné relace služby Vzdálená plocha**a klepněte na příkaz **Upravit**.

1. Zadejte následující nastavení a klepněte na tlačítko **OK**:
   1. Vyberte **Povoleno**.
   1. V části **Možnosti**zadejte **limit nečinnosti relace**.

    ![Limit nečinné relace](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. Nakonec chcete-li kombinovat toto chování s **automatickým vypnutím při nastavení odpojení,** postupujte podle pokynů v článku s postupy: [Povolit automatické vypnutí virtuálních počítačů při odpojení](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect).

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Příkaz Odebrat vypnutí systému Windows z nabídky Start

Nastavení **místních zásad skupiny systému** Windows také umožňuje odebrat příkaz vypnutí z nabídky **Start.**

Chcete-li odebrat příkaz vypnutí, můžete se připojit k virtuálnímu počítače šablony a spustit níže uvedený skript Prostředí PowerShell.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Nebo můžete postupovat podle těchto ručních kroků pomocí šablony virtuálního počítače:

1. Stiskněte klávesu Windows, zadejte **příkaz gpedit**a vyberte **možnost Upravit zásady skupiny (Ovládací panely).**

1. Přejděte na **položku Konfigurace počítače > šablony pro správu > nabídky Start a hlavního panelu**.  

    ![Editor zásad místní skupiny](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Klepněte pravým tlačítkem myši na **odebrat příkazy Vypnout, Restartovat, Režim spánku a Hibernaci**a zabránit jim v přístupu a klepněte na příkaz **Upravit**.

1. Vyberte nastavení **Povoleno** a klepněte na **tlačítko OK**:
 
   ![Nastavení vypnutí](../media/how-to-windows-shutdown/edit-shutdown.png)

1. Všimněte si, že příkaz vypnutí se již nezobrazuje v nabídce **Start** systému Windows; zobrazí se pouze příkaz **Odpojit.**

    ![Vypnutí, příkaz](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Další kroky
Přečtěte si článek o přípravě virtuálního počítače šablony Windows: [Průvodce nastavením počítače se šablonami Windows ve službě Azure Lab Services](how-to-prepare-windows-template.md)