---
title: Příručka pro řízení chování při vypínání Windows v Azure Lab Services | Microsoft Docs
description: Postup automatického vypnutí nečinného virtuálního počítače s Windows a odebrání příkazu pro vypnutí systému Windows.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: 248bbeabaf704ba636e2f82c7a93d0ee90a09f22
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94647694"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Průvodce řízením chování při vypínání Windows

Azure Lab Services poskytuje několik řízení nákladů, které zajistí, že virtuální počítače s Windows nejsou neočekávaně spuštěné:
 - [Nastavit plán](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
 - [Nastavit kvóty pro uživatele](./how-to-configure-student-usage.md#set-quotas-for-users)
 - [Povolení automatického vypnutí při odpojení](./how-to-enable-shutdown-disconnect.md)

I s těmito ovládacími prvky nákladů existují situace, kdy se virtuální počítač s Windows může nečekaně spustit; a v důsledku toho odečte z kvóty studenta:

- **Okno RDP je ponecháno otevřené**
  
    Když se student připojí ke svému virtuálnímu počítači pomocí protokolu RDP, může nechtěně ponechat okno RDP otevřené.  Dokud zůstane okno RDP otevřené, nastavení **automatického vypnutí při odpojení** se nikdy neprojeví, protože se aktivuje jenom po odpojení relace RDP.

- **Příkaz pro vypnutí systému Windows slouží k vypnutí virtuálního počítače.**
  
    Student může použít příkaz pro vypnutí systému Windows nebo jiné mechanismy vypnutí poskytované v rámci systému Windows k vypnutí virtuálního počítače místo použití [tlačítka zastavit Azure Lab Services](./how-to-use-classroom-lab.md#start-or-stop-the-vm).  V takovém případě se virtuální počítač stále používá v perspektivě Azure Lab Services.
    
V této příručce najdete kroky pro automatické vypnutí nečinného virtuálního počítače s Windows a odebrání příkazu pro vypnutí Windows z nabídky **Start** .  

> [!NOTE]
> Virtuální počítač se také může nečekaně odečíst od kvóty, když Student spustí svůj virtuální počítač, ale nikdy se k němu nepřipojí pomocí protokolu RDP.  Tato *Příručka aktuálně tento scénář neřeší.*  Místo toho by se měli po jeho spuštění na studenty připojit ke svému virtuálnímu počítači hned pomocí protokolu RDP; nebo by měl virtuální počítač zastavit.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>Odebrat příkaz pro vypnutí Windows z nabídky Start

Nastavení **místní zásady skupiny** systému Windows vám také umožní odebrat příkaz pro vypnutí z nabídky **Start** .

K odebrání příkazu pro vypnutí se můžete připojit k virtuálnímu počítači šablony a spustit pod ním skript prostředí PowerShell.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

Nebo se můžete rozhodnout, že budete postupovat podle těchto ručních kroků pomocí šablony VM:

1. Stiskněte klávesu Windows, zadejte **gpedit** a pak vyberte **upravit zásady skupiny (ovládací panely)**.

1. Přejděte na **Konfigurace počítače > Šablony pro správu > nabídce Start a na hlavním panelu**.  

    ![Editor místních zásad skupiny](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. Klikněte pravým tlačítkem na **Odebrat a zabraňte přístup k příkazům pro vypnutí, restartování, spánku a hibernaci** a klikněte na **Upravit**.

1. Vyberte nastavení **povoleno** a pak klikněte na **OK**:
 
   ![Nastavení vypnutí](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Všimněte si, že příkaz pro vypnutí se již nezobrazuje v nabídce **Start** systému Windows. zobrazí se pouze příkaz **Odpojit** .

    ![Shutdown – příkaz](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>Další kroky
Přečtěte si článek o tom, jak připravit virtuální počítač s Windows šablonou: [Průvodce nastavením počítače šablony Windows v Azure Lab Services](how-to-prepare-windows-template.md)