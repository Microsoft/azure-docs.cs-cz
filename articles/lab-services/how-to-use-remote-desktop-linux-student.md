---
title: Připojení k virtuálnímu počítači se systémem Linux v Azure Lab Services | Microsoft Docs
description: Naučte se používat vzdálenou plochu pro virtuální počítače se systémem Linux v testovacím prostředí v Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d13868477ff2e3378d87d7785789a7498ed17e59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85443413"
---
# <a name="connect-to-linux-virtual-machines-in-a-classroom-lab-of-azure-lab-services"></a>Připojení k virtuálním počítačům se systémem Linux v laboratoři učebny Azure Lab Services
Tento článek ukazuje, jak se můžou studenti připojit k virtuálnímu počítači se systémem Linux v testovacím prostředí pomocí:
- Terminál SSH (Secure Shell Protocol)
- GUI (grafické uživatelské rozhraní) Vzdálená plocha

> [!IMPORTANT] 
> SSH se automaticky nakonfiguruje tak, aby studenti i instruktor mohli jednat na virtuální počítače se systémem Linux bez jakéhokoli dalšího nastavení. Pokud se ale studenti potřebují připojit k používání vzdálené plochy s grafickým uživatelským rozhraním, může instruktor potřebovat další nastavení.  Podrobnosti najdete v tématu [Povolení služby Vzdálená plocha pro virtuální počítače se systémem Linux](how-to-enable-remote-desktop-linux.md).

## <a name="connect-to-the-student-vm-using-ssh"></a>Připojení k virtuálnímu počítači studenta pomocí SSH

1. Když se student přihlašuje přímo k portálu Labs ( `https://labs.azure.com` ) nebo pomocí odkazu na registraci ( `https://labs.azure.com/register/<registrationCode>` ), zobrazí se dlaždice pro každé testovací prostředí, ke kterému má student přístup. 
   
1. Na dlaždici přepněte tlačítko tak, aby se virtuální počítač spustil, pokud je v zastaveném stavu. 

2. Vyberte **Připojit**. Zobrazí se dvě možnosti, jak se připojit k virtuálnímu počítači: **SSH** a **RDP**.

    ![Virtuální počítač student – možnosti připojení](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)

3. Vyberte možnost **SSH** a zobrazí se dialogové okno **připojit k virtuálnímu počítači** :  

    ![Připojovací řetězec SSH](./media/how-to-enable-remote-desktop-linux/ssh-connection-string.png)

4. Kliknutím na tlačítko **Kopírovat** vedle textového pole zkopírujte informace o připojení SSH do schránky. 

5. Uložte informace o připojení SSH, například v textovém panelu, abyste mohli použít tyto informace o připojení v dalším kroku.

6. Z terminálu SSH [(jako např](https://www.putty.org/).) se připojte k virtuálnímu počítači.

## <a name="connect-to-the-student-vm-using-gui-remote-desktop"></a>Připojení k virtuálnímu počítači studenta pomocí uživatelské rozhraní Remote Desktop
Instruktor se může rozhodnout nakonfigurovat virtuální počítače, aby se studenti mohli připojit i pomocí vzdálené plochy grafického uživatelského rozhraní.  V takovém případě musí studenti zjistit od svého instruktora, jestli se k virtuálním počítačům připojovat pomocí klientské aplikace **Vzdálená plocha Microsoft (RDP)** nebo klienta **X2Go** .  Obě tyto aplikace umožňují, aby se student vzdáleně připojil ke svému VIRTUÁLNÍmu počítači a zobrazil na svém místním počítači Grafická plocha Linux.

### <a name="connect-to-the-student-vm-using-microsoft-remote-desktop-rdp"></a>Připojení k virtuálnímu počítači studenta pomocí Vzdálená plocha Microsoft (RDP)
Studenti můžou použít Vzdálená plocha Microsoft (RDP) pro připojení k virtuálním počítačům se systémem Linux poté, co instruktor nastaví své testovací prostředí s balíčky RDP a GUI pro grafické prostředí systému Linux (například důstojník, desktop Xfce atd.). Postup pro připojení: 

1. Na dlaždici pro virtuální počítač zkontrolujte, že je virtuální počítač spuštěný, a klikněte na **připojit**. Zobrazí se dvě možnosti, jak se připojit k virtuálnímu počítači: **SSH** a **RDP**.

    ![Virtuální počítač student – možnosti připojení](./media/how-to-enable-remote-desktop-linux/student-vm-connect-options.png)
2. Vyberte možnost **RDP** .  Po stažení souboru RDP na váš počítač ho uložte do svého virtuálního počítače.

3. Pokud se připojujete z počítače se systémem Windows, klient Vzdálená plocha Microsoft (RDP) je již nainstalován a nakonfigurován.  V důsledku toho stačí k otevření souboru RDP kliknout na něj a spustit vzdálenou relaci.

    Pokud se připojujete z počítače Mac nebo Chromebook, přečtěte si následující postup:
   - [Připojte se k virtuálnímu počítači pomocí protokolu RDP na Macu](connect-virtual-machine-mac-remote-desktop.md).
   - [Připojte se k virtuálnímu počítači pomocí protokolu RDP na Chromebook](connect-virtual-machine-chromebook-remote-desktop.md).  

### <a name="connect-to-the-student-vm-using-x2go"></a>Připojení k virtuálnímu počítači studenta pomocí X2Go
Studenti můžou pomocí X2Go připojit se ke svým virtuálním počítačům se systémem Linux poté, co instruktor nastaví své testovací prostředí s X2Go a balíčky grafického uživatelského rozhraní pro grafické prostředí systému Linux (například důstojník, desktop Xfce atd.).

Studenti potřebují zjistit od svého instruktora, který používá grafické prostředí pro Linux, ve kterém je nainstalovaný jeho instruktor.  Tyto informace jsou potřeba v dalších krocích pro připojení pomocí klienta X2Go.

1. Nainstalujte [klienta X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) do místního počítače.

1. Podle pokynů v [první části](how-to-use-remote-desktop-linux-student.md#connect-to-the-student-vm-using-ssh) zkopírujte informace o připojení SSH pro váš virtuální počítač.  Tyto informace budete potřebovat pro připojení pomocí klienta X2Go.

1. Jakmile budete mít informace o připojení SSH, otevřete klienta X2Go a vyberte **relace**  >  **Nová relace**.
   ![X2Go vytvořit novou relaci](./media/how-to-use-classroom-lab/x2go-new-session.png)

1. Zadejte hodnoty v podokně **Předvolby relace** na základě informací o připojení SSH.  Například vaše informace o připojení budou vypadat podobně jako v tomto příkladu:

    ```bash
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
    ```

    V tomto příkladu jsou zadány následující hodnoty:

   - **Název relace** – zadejte název, jako je název vašeho virtuálního počítače.
   - **Host** – ID virtuálního počítače; například **`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`** .
   - **Přihlášení** – uživatelské jméno pro váš virtuální počítač; například **student**.
   - **Port SSH** – jedinečný port přiřazený k vašemu virtuálnímu počítači; například **12345**.
   - **Typ relace** – vyberte grafické prostředí pro Linux, ve kterém váš instruktor nakonfiguroval váš virtuální počítač.  Tyto informace musíte získat od svého instruktora.

    Nakonec kliknutím na tlačítko **OK** vytvořte relaci.

    ![Předvolby relace X2Go](./media/how-to-use-classroom-lab/x2go-session-preferences.png)

1.  V pravém podokně klikněte na svou relaci.

    ![X2Go začít novou relaci](./media/how-to-use-classroom-lab/x2go-start-session.png)

    > [!NOTE] 
    > Pokud se zobrazí výzva s podobnou zprávou, vyberte **Ano** , pokud chcete pokračovat v zadávání hesla: **pravost hostitele [ `00000000-0000-0000-0000-000000000000.eastus2.cloudapp.eastus.cloudapp.azure.com` ]: 12345 nejde navázat.  ECDSA klíčového otisku je SHA256:00000000000000000000000000000000000000000000. určitě chcete pokračovat v připojování (ano/ne)?**

2. Po zobrazení výzvy zadejte své heslo a klikněte na **OK**.  Teď se budete vzdáleně připojovat k prostředí klasického uživatelského rozhraní (GUI) vašeho virtuálního počítače.

## <a name="next-steps"></a>Další kroky
Informace o tom, jak povolit funkci připojení ke vzdálené ploše pro virtuální počítače se systémem Linux v prostředí učebny, najdete v tématu [Povolení vzdálené plochy pro virtuální počítače se systémem Linux](how-to-enable-remote-desktop-linux.md). 

