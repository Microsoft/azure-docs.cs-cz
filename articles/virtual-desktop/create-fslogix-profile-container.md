---
title: Kontejnery profilů FSLogix NetApp virtuální plocha Windows – Azure
description: Postup vytvoření kontejneru profilu FSLogix pomocí Azure NetApp Files ve virtuálním počítači s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6a9f2c62d8e7f17f6ea8377982c79fef3dfbb97c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96016824"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Vytvoření kontejneru profilu pomocí Azure NetApp Files a služba AD DS

Pro [službu Virtual Desktop systému Windows](overview.md)doporučujeme používat kontejnery profilů FSLogix jako řešení uživatelských profilů. Kontejnery profilů FSLogix ukládají úplný profil uživatele v jednom kontejneru a jsou navržené pro roaming profilů v netrvalých vzdálených výpočetních prostředích, jako je třeba virtuální počítač s Windows. Když se přihlásíte, kontejner se dynamicky připojí k výpočetnímu prostředí pomocí místně podporovaného virtuálního pevného disku (VHD) a virtuálního pevného disku Hyper-V (VHDX). Tyto rozšířené technologie Filter-Driver umožňují, aby byl profil uživatele hned dostupný a byl zobrazen v systému, stejně jako místní profil uživatele. Další informace o kontejnerech profilů FSLogix najdete v tématu [kontejnery profilů FSLogix a soubory Azure](fslogix-containers-azure-files.md).

Kontejnery profilů FSLogix můžete vytvářet pomocí [Azure NetApp Files](https://azure.microsoft.com/services/netapp/), snadno použitelné služby Azure Native Platform, která zákazníkům pomáhá rychle a spolehlivě ZŘIZOVAT svazky SMB na podnikové úrovni pro prostředí virtuálních ploch Windows. Další informace o Azure NetApp Files najdete v tématu [co je Azure NetApp Files?](../azure-netapp-files/azure-netapp-files-introduction.md)

V této příručce se dozvíte, jak nastavit účet Azure NetApp Files a vytvořit kontejnery profilů FSLogix ve virtuálním počítači s Windows.

V tomto článku se předpokládá, že už máte [fondy hostitelů](create-host-pools-azure-marketplace.md) nastavené a seskupené do jednoho nebo víc tenantů v prostředí virtuálních počítačů s Windows. Informace o tom, jak nastavit klienty, najdete v tématu [Vytvoření tenanta v rámci virtuálního počítače s Windows](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) a [našeho příspěvku na blogu pro technickou komunitu](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

Pokyny v této příručce jsou určené konkrétně pro uživatele s virtuálními počítači s Windows. Pokud hledáte obecnější informace o tom, jak nastavit Azure NetApp Files a vytvořit kontejnery profilů FSLogix mimo virtuální počítač s Windows, přečtěte si téma [nastavení Azure NetApp Files a vytvoření svazku NFS v rychlém](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)startu.

>[!NOTE]
>Tento článek se nezabývá osvědčenými postupy pro zabezpečení přístupu ke sdílené Azure NetApp Files.

>[!NOTE]
>Pokud hledáte srovnávací materiál o různých možnostech úložiště kontejneru FSLogix v Azure, přečtěte si téma [Možnosti úložiště pro kontejnery profilů FSLogix](store-fslogix-profile.md).

## <a name="prerequisites"></a>Požadavky

Než budete moct vytvořit kontejner profilu FSLogix pro fond hostitelů, musíte:

- Nastavení a konfigurace virtuálního počítače s Windows
- Zřízení fondu hostitelů virtuálních počítačů s Windows
- [Povolení předplatného Azure NetApp Files](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Nastavení účtu Azure NetApp Files

Abyste mohli začít, musíte nastavit účet Azure NetApp Files.

1. Přihlaste se na [Azure Portal](https://portal.azure.com). Ujistěte se, že váš účet má oprávnění přispěvatele nebo správce.

2. Vyberte **ikonu Azure Cloud Shell** napravo od panelu hledání a otevřete Azure Cloud Shell.

3. Po otevření Azure Cloud Shell vyberte **PowerShell**.

4. Pokud Azure Cloud Shell používáte poprvé, vytvořte účet úložiště ve stejném předplatném, ve kterém zachováte Azure NetApp Files a virtuální plochu Windows.

   > [!div class="mx-imgBorder"]
   > ![Okno účtu úložiště s tlačítkem vytvořit úložiště v dolní části okna zvýrazněné červeně.](media/create-storage-button.png)

5. Jakmile Azure Cloud Shell načte, spusťte následující dvě rutiny.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. V levé části okna vyberte **všechny služby**. Do vyhledávacího pole, které se zobrazí v horní části nabídky, zadejte **Azure NetApp Files** .

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky uživatele, který zadává slovo "Azure NetApp Files" do vyhledávacího pole všechny služby. Ve výsledcích hledání se zobrazuje prostředek Azure NetApp Files.](media/azure-netapp-files-search-box.png)


7. Ve výsledcích hledání vyberte **Azure NetApp Files** a pak vyberte **vytvořit**.

8. Vyberte tlačítko **Přidat** .
9. Po otevření karty **nového účtu NetApp** zadejte následující hodnoty:

    - Jako **název** zadejte název účtu NetApp.
    - V poli **předplatné** vyberte předplatné pro účet úložiště, které jste nastavili v kroku 4 z rozevírací nabídky.
    - V poli **Skupina prostředků** vyberte z rozevírací nabídky existující skupinu prostředků nebo vytvořte novou výběrem možnosti **vytvořit nový**.
    - V poli **umístění** vyberte oblast účtu NetApp z rozevírací nabídky. Tato oblast musí být stejná jako vaše virtuální počítače hostitele relace.

   >[!NOTE]
   >Azure NetApp Files aktuálně nepodporuje připojení svazku mezi oblasti.

10. Až budete hotovi, vyberte **vytvořit** a vytvořte účet NetApp.

## <a name="create-a-capacity-pool"></a>Vytvoření fondu kapacity

Pak vytvořte nový fond kapacity:

1. Přejděte do nabídky Azure NetApp Files a vyberte svůj nový účet.
2. V nabídce účtu v části služba úložiště vyberte **fondy kapacit** .
3. Vyberte **Přidat fond**.
4. Po otevření karty **Nový fond kapacit** zadejte následující hodnoty:

    - Do pole **název** zadejte název nového fondu kapacity.
    - V rozevírací nabídce vyberte požadovanou hodnotu pro **úroveň služby**. Pro většinu prostředí doporučujeme **Premium** .
       >[!NOTE]
       >Nastavení Premium poskytuje minimální propustnost dostupnou pro úroveň Premium Service, která je 256 MB/s. Možná budete muset upravit tuto propustnost pro produkční prostředí. Konečná propustnost vychází z vztahu popsaného v části [omezení propustnosti](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - V poli **Velikost (TIB)** zadejte velikost fondu kapacit, který nejlépe vyhovuje vašim potřebám. Minimální velikost je 4 TiB.

5. Až to budete mít, vyberte **OK**.

## <a name="join-an-active-directory-connection"></a>Připojení ke službě Active Directory

Potom musíte připojit připojení ke službě Active Directory.

1. V nabídce na levé straně stránky vyberte **připojení služby Active Directory** a pak vyberte tlačítko **připojit** a otevřete stránku připojit se ke **službě Active Directory** .

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s nabídkou připojení ke službě Active Directory](media/active-directory-connections-menu.png)

2. Zadejte následující hodnoty na stránce připojit se ke **službě Active Directory** , abyste se připojili k připojení:

    - V případě **primárního serveru DNS** zadejte IP adresu serveru DNS ve vašem prostředí, která může přeložit název domény.
    - V případě **domény** zadejte plně kvalifikovaný název domény (FQDN).
    - V poli **předpona serveru SMB (účet počítače)** zadejte řetězec, který chcete připojit k názvu účtu počítače.
    - Jako **uživatelské jméno** zadejte název účtu s oprávněním k provedení připojení k doméně.
    - Jako **heslo** zadejte heslo účtu.

## <a name="create-a-new-volume"></a>Vytvořit nový svazek

V dalším kroku budete muset vytvořit nový svazek.

1. Vyberte **svazky** a pak vyberte **Přidat svazek**.

2. Po otevření karty **vytvořit svazek** zadejte následující hodnoty:

    - Jako **název svazku** zadejte název nového svazku.
    - V poli **fond kapacit** vyberte fond kapacit, který jste právě vytvořili, z rozevírací nabídky.
    - Pro **kvótu (GIB)** zadejte velikost svazku, která je vhodná pro vaše prostředí.
    - V případě **virtuální sítě** vyberte existující virtuální síť, která má připojení k řadiči domény z rozevírací nabídky.
    - V části **podsíť** vyberte **vytvořit novou**. Pamatujte, že tato podsíť bude delegována na Azure NetApp Files.

3.  Výběrem **Další: protokol \> \>** otevřete kartu protokol a nakonfigurujte parametry přístupu ke svazku.

## <a name="configure-volume-access-parameters"></a>Konfigurace parametrů přístupu ke svazkům

Po vytvoření svazku nakonfigurujte parametry přístupu ke svazku.

1.  Jako typ protokolu vyberte **SMB** .
2.  V rozevírací nabídce konfigurace v **Active Directory** vyberte stejný adresář, ke kterému jste se připojili, a připojte se k [připojení Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection). Mějte na paměti, že u každého předplatného je omezení jedné služby Active Directory.
3.  Do textového pole **název sdílené složky** zadejte název sdílené složky používané fondem hostitelů relací a jeho uživateli.

4.  V dolní části stránky vyberte **zkontrolovat + vytvořit** . Tím se otevře stránka ověřování. Po úspěšném ověření svazku vyberte **vytvořit**.

5.  V tuto chvíli se nový svazek začne nasazovat. Po dokončení nasazení můžete použít sdílenou složku Azure NetApp Files.

6.  Pokud chcete zobrazit cestu pro připojení, vyberte **Přejít k prostředku** a podívejte se na kartu Přehled.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s přehledem s červenou šipkou ukazující na cestu pro připojení](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Konfigurace FSLogix na virtuálních počítačích hostitele relace

Tato část je založená na [vytvoření kontejneru profilu pro fond hostitelů pomocí sdílené složky](create-host-pools-user-profile.md).

1. [Stáhněte si soubor. zip s agentem FSLogix](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) a pořád jste ve VZDÁLENÉm virtuálním počítači hostitele relace.

2. Extrahování staženého souboru.

3. V souboru, přejdete na **verze x64**  >   a spusťte **FSLogixAppsSetup.exe**. Otevře se nabídka instalace.

4.  Pokud máte kód Product Key, zadejte ho do textového pole kód Product Key.

5. Zaškrtněte políčko vedle Souhlasím **s licenčními podmínkami a ujednáními**.

6. Vyberte **Nainstalovat**.

7. Přejděte do **C: \\ Program Files \\ FSLogix \\ Apps** a potvrďte, že je agent nainstalovaný.

8. V nabídce Start spusťte program **Regedit** jako správce.

9. Přejděte na **počítač \\ HKEY_LOCAL_MACHINE \\ software \\ FSLogix**.

10. Vytvořte klíč s názvem **Profiles**.

11.  Vytvořte hodnotu s názvem **Enabled** s typem **REG_DWORD** nastavenou na hodnotu dat **1**.

12. Vytvořte hodnotu s názvem **VHDLocations** s typem s **více řetězci** a nastavte její datovou hodnotu na URI pro sdílenou složku Azure NetApp Files.

13. Vytvořte hodnotu s názvem **DeleteLocalProfileWhenVHDShouldApply** s hodnotou DWORD 1, abyste zabránili problémům se stávajícími místními profily před přihlášením.

     >[!WARNING]
     >Při vytváření hodnoty DeleteLocalProfileWhenVHDShouldApply buďte opatrní. Když systém profilů FSLogix určuje, že uživatel musí mít profil FSLogix, ale místní profil už existuje, kontejner profilu trvale odstraní místní profil. Uživatel pak bude přihlášený pomocí nového profilu FSLogix.

## <a name="assign-users-to-session-host"></a>Přiřazení uživatelů k hostiteli relace

1. Otevřete **POWERSHELL ISE** jako správce a přihlaste se k virtuálnímu počítači s Windows.

2. Spusťte následující rutiny:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Až se zobrazí výzva k zadání přihlašovacích údajů, zadejte přihlašovací údaje pro uživatele s rolemi tvůrce tenanta nebo RDS Owner/RDS na tenantovi virtuálních klientů Windows.

4. Spuštěním následujících rutin přiřaďte uživatele ke skupině vzdálené plochy:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Ujistěte se, že uživatelé mají přístup ke sdílené složce Azure NetApp.

1. Otevřete internetový prohlížeč a pokračujte na <https://rdweb.wvd.microsoft.com/arm/webclient> .

2. Přihlaste se pomocí přihlašovacích údajů uživatele přiřazeného ke skupině Vzdálená plocha.

3. Po navázání uživatelské relace se přihlaste k Azure Portal pomocí účtu správce.

4. Otevřete **Azure NetApp Files**, vyberte svůj účet Azure NetApp Files a pak vyberte **svazky**. Po otevření nabídky svazky vyberte odpovídající svazek.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s NetApp účtem, který jste nastavili dříve v Azure Portal s vybraným tlačítkem svazky.](media/netapp-account.png)

5. Přejít na kartu **Přehled** a ověřte, že kontejner profilu FSLogix používá místo.

6. Připojte se přímo k libovolné části virtuálního počítače fondu hostitele pomocí vzdálené plochy a otevřete **Průzkumníka souborů.** Pak přejděte do **cesty pro připojení** (v následujícím příkladu se jedná o cestu pro připojení \\ \\ ANF-SMB-3863.gt1107.onmicrosoft.com \\ ANF-VOL).

   V rámci této složky by měl existovat profil VHD (nebo VHDX), jako je ten v následujícím příkladu.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky obsahu složky v cestě pro připojení Uvnitř je jeden soubor VHD s názvem "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Další kroky

Kontejnery profilů FSLogix můžete použít k nastavení sdílené složky profilu uživatele. Informace o tom, jak vytvořit sdílené složky profilů uživatelů pomocí nových kontejnerů, najdete v tématu [vytvoření kontejneru profilů pro fond hostitelů pomocí sdílené složky](create-host-pools-user-profile.md).

Můžete také vytvořit sdílenou složku souborů Azure, do které se uloží profil FSLogix. Další informace najdete v tématu [Vytvoření sdílené složky souborů Azure s řadičem domény](create-file-share.md).