---
title: Instalace kontejnerů aplikací systém Microsoft Office FSLogix na virtuálním počítači s Windows – Azure
description: Jak pomocí editoru pravidel aplikací vytvořit kontejner aplikace FSLogix s Office ve virtuálním počítači s Windows
author: Heidilohr
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: a144adcfbf6c7cefc6b946f95bdb734868de801f
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446804"
---
# <a name="install-microsoft-office-using-fslogix-application-containers"></a>Instalace systém Microsoft Office s využitím kontejnerů aplikací FSLogix

Systém Microsoft Office můžete rychle a efektivně nainstalovat pomocí kontejneru aplikace FSLogix jako šablony pro ostatní virtuální počítače ve fondu hostitelů.

V takovém případě může použití kontejneru aplikace FSLogix pomoci při rychlejší instalaci:

- Přesměrování aplikací Office do kontejneru aplikací snižuje požadavky na velikost jednotky C.
- Snímky nebo zálohy virtuálního počítače pobírají méně prostředků.
- Automatický kanál prostřednictvím aktualizace jedné image usnadňuje aktualizaci vašich virtuálních počítačů.
- K instalaci Office (a dalším aplikacím) potřebujete jenom jednu Image na všechny virtuální počítače v nasazení virtuálních počítačů s Windows.

V tomto článku se dozvíte, jak nastavit kontejner aplikace FSLogix pomocí Office.

## <a name="requirements"></a>Požadavky

K nastavení editoru pravidel budete potřebovat následující věci:

- virtuální počítač s nainstalovaným systémem Windows bez sady Office
- kopie Office
- kopie FSLogix nainstalované v nasazení
- sdílená síťová složka, ke které mají všechny virtuální počítače ve fondu hostitelů přístup jen pro čtení

## <a name="install-office"></a>Nainstalovat Office

Pokud chcete nainstalovat Office na VHD nebo VHDX, povolte na svém VIRTUÁLNÍm počítači protokol RDP (Remote Desktop Protocol) a pak postupujte podle pokynů v části [instalace Office na hlavní IMAGI VHD](install-office-on-wvd-master-image.md). Při instalaci nástroje se ujistěte, že používáte [správné licence](overview.md#requirements).

>[!NOTE]
>Virtuální plocha Windows vyžaduje aktivaci počítače s SCA (Shared Computer Activation).

## <a name="install-fslogix"></a>Nainstalovat FSLogix

Pokud chcete nainstalovat FSLogix a editor pravidel, postupujte podle pokynů v tématu [Stažení a instalace FSLogix](/fslogix/install-ht).

## <a name="create-and-prepare-a-vhd-to-store-office"></a>Vytvoření a příprava VHD pro uložení Office

V dalším kroku budete muset vytvořit a připravit image VHD, na které se má Editor pravidel použít:

1. Otevřete příkazový řádek jako správce. a spusťte následující příkaz:

    ```cmd
        taskkill /F /IM MicrosoftEdge.exe /T
    ```

    >[!NOTE]
    > Nezapomeňte ponechat prázdné mezery, které vidíte v tomto příkazu.

2. Dále spusťte následující příkaz:

    ```cmd
    sc queryex type=service state=all | find /i "ClickToRunSvc"
    ```
    
   Pokud službu najdete, restartujte virtuální počítač a teprve potom pokračujte krokem 3.

    ```cmd
    net stop ClickToRunSvc
    ```

3. Pak přejdete na **soubory Program Files**  >  **FSLogix**  >   a spuštěním následujícího příkazu vytvořte cílový virtuální pevný disk:

    ```cmd
    frx moveto-vhd -filename <path to network share>\office.vhdx -src "C:\Program Files\Microsoft Office" -size-mbs 5000 
    ```

    Virtuální pevný disk, který vytvoříte pomocí tohoto příkazu, by měl obsahovat složku C: \\ Program Files \\ systém Microsoft Office.

    >[!NOTE]
    >Pokud se zobrazí nějaké chyby, odinstalujte Office a začněte znovu z kroku 1.

## <a name="configure-the-rule-editor"></a>Konfigurace editoru pravidel

Teď, když jste připravili image, budete muset nakonfigurovat Editor pravidel a vytvořit soubor pro uložení pravidel v nástroji.

1. Přejít na **Program Files**  >  **FSLogix**  >  **Apps** a spusťte **RuleEditor.exe**.

2. Vyberte **soubor**  >  **Nový**  >  **vytvořit** , chcete-li vytvořit novou sadu pravidel, a poté uložte tuto sadu pravidel do místní složky.

3. Vyberte možnost **prázdná sada pravidel** a pak vyberte **OK**.

4. Vyberte tlačítko **+**. Otevře se okno **Přidat pravidlo** . Tím se změní možnosti v dialogovém okně **Přidat pravidlo** .

5. V rozevírací nabídce vyberte **pravidlo kontejneru aplikací (VHD)**.

6. Do pole **Složka** zadejte **C: \\ Program Files \\ systém Microsoft Office** .

7. V části **soubor disku** vyberte **\<path\> \\ Office. VHD** z oddílu **vytvořit cílový virtuální pevný disk** .

8. Vyberte **OK**.

9. V části pracovní složka na adrese **C: \\ Uživatelé \\ \<username\> \\ dokumenty \\ FSLogix sady pravidel** a vyhledejte soubory. FRx a. fxa. Tyto soubory je potřeba přesunout do složky pravidel ve složce **C: \\ Program Files \\ FSLogix \\ Apps \\ rules** , aby pravidla mohla začít pracovat.

10. Vyberte **použít pravidla pro systém** , aby se pravidla projevila.

     >[!NOTE]
     > Bude nutné použít soubory pravidel aplikace pro všechny hostitele relací.

## <a name="next-steps"></a>Další kroky

Pokud chcete získat další informace o FSLogix, podívejte se na naši [dokumentaci k FSLogix](/fslogix/).