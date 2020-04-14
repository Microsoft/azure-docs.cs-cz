---
title: Kontejnery profilů FSLogix NetApp Virtuální plocha Windows – Azure
description: Jak vytvořit kontejner profilu FSLogix pomocí souborů Azure NetApp ve Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 149fb5818ee360c7333997655ea9eb8d7ded346c
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81270889"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Vytvoření kontejneru profilu FSLogix pro fond hostitelů pomocí souborů Azure NetApp

Doporučujeme používat kontejnery profilů FSLogix jako řešení profilu uživatele pro [službu Windows Virtual Desktop](overview.md). Kontejnery profilů FSLogix ukládají kompletní profil uživatele do jednoho kontejneru a jsou navrženy tak, aby se přemítaly o profily v netrvalých vzdálených výpočetních prostředích, jako je virtuální plocha systému Windows. Při přihlášení se kontejner dynamicky připojí k výpočetnímu prostředí pomocí místně podporovaného virtuálního pevného disku (VHD) a virtuálního pevného disku Hyper-V (VHDX). Tyto pokročilé technologie ovladače filtru umožňují, aby byl profil uživatele okamžitě k dispozici a objevil se v systému přesně jako místní uživatelský profil. Další informace o kontejnerech profilů FSLogix najdete v [tématu Kontejnery profilů FSLogix a soubory Azure](fslogix-containers-azure-files.md).

Kontejnery profilů FSLogix můžete vytvářet pomocí [Azure NetApp Files](https://azure.microsoft.com/services/netapp/), snadno použitelné služby nativní platformy Azure, která zákazníkům pomáhá rychle a spolehlivě zřazovat svazky SMB na podnikové úrovni pro prostředí Virtuální chodWindows. Další informace o souborech Azure NetApp najdete v tématu [Co je soubory Azure NetApp?](../azure-netapp-files/azure-netapp-files-introduction.md)

Tato příručka vám ukáže, jak nastavit účet Azure NetApp Files a vytvořit kontejnery profilů FSLogix ve Windows Virtual Desktop.

Tento článek předpokládá, že již máte [fondy hostitelů](create-host-pools-azure-marketplace.md) nastavené a seskupené do jednoho nebo více klientů v prostředí Virtuální plochy systému Windows. Informace o tom, jak nastavit klienty, najdete [v tématu Vytvoření tenanta ve Windows Virtual Desktop](tenant-setup-azure-active-directory.md) a v našem [příspěvku na blogu Technické komunity](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

Pokyny v této příručce jsou určeny konkrétně uživatelům virtuální chod windows. Pokud hledáte obecnější pokyny, jak nastavit soubory Azure NetApp a vytvořit kontejnery profilů FSLogix mimo Windows Virtual Desktop, přečtěte [si seznam Nastavení souborů Azure NetApp a vytvoření rychlého spuštění svazku systému souborů NFS](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>Tento článek se nevztahuje na osvědčené postupy pro zabezpečení přístupu ke sdílené složce Azure NetApp Files.

>[!NOTE]
>Pokud hledáte srovnávací materiál o různých možnostech úložiště kontejneru profilů FSLogix v Azure, [přečtěte si informace o možnostech úložiště pro kontejnery profilů FSLogix](store-fslogix-profile.md).

## <a name="prerequisites"></a>Požadavky

Před vytvořením kontejneru profilu FSLogix pro fond hostitelů je nutné:

- Nastavení a konfigurace virtuální plochy systému Windows
- Zřízení fondu hostitelů virtuální plochy systému Windows
- [Povolení předplatného souborů Azure NetApp](../azure-netapp-files/azure-netapp-files-register.md)

## <a name="set-up-your-azure-netapp-files-account"></a>Nastavení účtu Azure NetApp Files

Chcete-li začít, musíte nastavit účet Azure NetApp Files.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Zkontrolujte, zda má váš účet oprávnění přispěvatele nebo správce.

2. Kliknutím na **ikonu Azure Cloud Shell** napravo od vyhledávacího panelu otevřete Azure Cloud Shell.

3. Po otevření Prostředí Azure Cloud Shell vyberte **PowerShell**.

4. Pokud azure cloud shell používáte poprvé, vytvořte si účet úložiště ve stejném předplatném, ve které budete mít soubory Azure NetApp a virtuální plochu Windows.

   ![Okno účtu úložiště s tlačítkem vytvořit úložiště v dolní části okna zvýrazněné červeně.](media/create-storage-button.png)

5. Jakmile se Azure Cloud Shell načte, spusťte následující dvě rutiny.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. V levé části okna vyberte **Všechny služby**. Zadejte **soubory Azure NetApp** do vyhledávacího pole, které se zobrazí v horní části nabídky.

   ![Snímek obrazovky uživatele, který zadává "Soubory Azure NetApp" do vyhledávacího pole Všechny služby. Výsledky hledání zobrazit prostředek Soubory Azure NetApp.](media/azure-netapp-files-search-box.png)


7. Ve výsledcích hledání vyberte **Soubory Azure NetApp** a pak vyberte **Vytvořit**.

8. Vyberte tlačítko **Přidat.**
9. Po otevření karty **Nový účet NetApp** zadejte následující hodnoty:

    - Do **pole Název**zadejte název účtu NetApp.
    - V **části Předplatné**vyberte v rozevírací nabídce předplatné pro účet úložiště, který jste nastavili v kroku 4.
    - Ve **skupině Prostředků**vyberte existující skupinu prostředků z rozevírací nabídky nebo vytvořte novou výběrem **možnosti Vytvořit nový**.
    - V **části Umístění**vyberte oblast svého účtu NetApp v rozevírací nabídce. Tato oblast musí být ve stejné oblasti jako virtuální počítače hostitele relace.

   >[!NOTE]
   >Soubory Azure NetApp aktuálně nepodporuje připojení svazku napříč oblastmi.

10. Až budete hotovi, vyberte **Vytvořit** a vytvořte si účet NetApp.

## <a name="create-a-capacity-pool"></a>Vytvoření fondu kapacity

Dále vytvořte nový fond kapacit: 

1. Přejděte do nabídky Soubory Azure NetApp a vyberte nový účet.
2. V nabídce účtu vyberte **Fondy kapacit v** části Úložiště.
3. Vyberte **Přidat fond**.
4. Po otevření karty **Fond nové kapacity** zadejte následující hodnoty:

    - Do **pole Název**zadejte název nového fondu kapacit.
    - V **nabídce Service Level**vyberte požadovanou hodnotu. Pro většinu prostředí doporučujeme **Premium.**
       >[!NOTE]
       >Nastavení Premium poskytuje minimální propustnost, která je k dispozici pro úroveň služby Premium, což je 256 MB/s. Možná budete muset upravit tuto propustnost pro produkční prostředí. Konečná propustnost je založena na vztahu popsaném v [mezích propustnost .](../azure-netapp-files/azure-netapp-files-service-levels.md)
    - V **části Velikost (TiB)** zadejte velikost fondu kapacity, která nejlépe vyhovuje vašim potřebám. Minimální velikost je 4 TiB.

5. Až to budete mít, vyberte **OK**.

## <a name="join-an-active-directory-connection"></a>Připojení k připojení služby Active Directory

Poté se musíte připojit k připojení služby Active Directory.

1. Vyberte **připojení služby Active Directory** v nabídce na levé straně stránky a pak vyberte tlačítko **Spojit** a otevřete stránku **Připojit službu Active Directory.**

   ![Snímek obrazovky s nabídkou Připojit se ke službě Připojení služby Active Directory](media/active-directory-connections-menu.png)

2. Chcete-li připojit připojení, zadejte na stránce **Připojit službu Active Directory** následující hodnoty:

    - V **části Primární služba DNS**zadejte adresu IP serveru DNS ve vašem prostředí, která může přeložit název domény.
    - Do **pole Doména**zadejte plně kvalifikovaný název domény (FQDN).
    - Pro **předponu SMB Server (Účet počítače)** zadejte řetězec, který chcete připojit k názvu účtu počítače.
    - Do **pole Uživatelské jméno**zadejte název účtu s oprávněními k provedení připojení k doméně.
    - V **části Heslo**zadejte heslo účtu.

## <a name="create-a-new-volume"></a>Vytvoření nového svazku

Dále budete muset vytvořit nový svazek.

1. Vyberte **Možnost Objemy**a pak vyberte **Přidat svazek**.

2. Po otevření karty **Vytvořit svazek** zadejte následující hodnoty:

    - Do **pole Název svazku**zadejte název nového svazku.
    - V **části Fond kapacit**vyberte fond kapacit, který jste právě vytvořili, v rozevírací nabídce.
    - V **části Kvóta (GiB)** zadejte velikost svazku vhodnou pro vaše prostředí.
    - V **části Virtuální síť**vyberte z rozevírací nabídky existující virtuální síť, která má připojení k řadiči domény.
    - V části **Podsíť**vyberte **Vytvořit nový**. Mějte na paměti, že tato podsíť bude delegována na soubory Azure NetApp.

3.  Vyberte **Další: Protokol \> ** otevřete kartu Protokol a nakonfigurujte parametry přístupu ke svazku.

## <a name="configure-volume-access-parameters"></a>Konfigurace parametrů přístupu ke svazku

Po vytvoření svazku nakonfigurujte parametry přístupu ke svazku.

1.  Jako typ protokolu vyberte **SMB.**
2.  V části Konfigurace v rozevírací nabídce **služby Active Directory** vyberte stejný adresář, který jste původně připojili [v okně Připojit se ke službě Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection). Nezapomeňte, že na jedno předplatné je limit jednoho služby Active Directory.
3.  Do textového pole **Sdílet název** zadejte název sdílené položky používané fondem hostitelů relací a jeho uživateli.

4.  V dolní části stránky vyberte **Zkontrolovat + vytvořit.** Otevře se stránka ověření. Po úspěšném ověření svazku vyberte **příkaz Vytvořit**.

5.  V tomto okamžiku se začne nasazovat nový svazek. Po dokončení nasazení můžete použít sdílenou složku Soubory Azure NetApp.

6.  Pokud chcete zobrazit cestu k připojení, vyberte **Přejít na zdroj** a vyhledejte ho na kartě Přehled.

    ![Snímek obrazovky Přehled s červenou šipkou ukazující na cestu připojení](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Konfigurace fslogixu na virtuálních počítačích hostitelských počítačů (VM)

Tato část je založena na [vytvoření kontejneru profilu pro fond hostitelů pomocí sdílené složky](create-host-pools-user-profile.md).

1. [Stáhněte si soubor .zip agenta FSLogix,](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) když jste stále vzdáleni ve virtuálním virtuálním virtuálním zařízení hostitele relace.

2. Rozbalte stažený soubor.

3. V souboru přejděte na **verzi x64** > **a** spusťte soubor **FSLogixAppsSetup.exe**. Otevře se instalační nabídka.

4.  Pokud máte kód Product Key, zadejte jej do textového pole Kód Product Key.

5. Zaškrtněte políčko vedle **Souhlasím s licenčními podmínkami**.

6. Vyberte **Install** (Nainstalovat).

7. Přejděte na **C:\\Program Files\\FSLogix\\Apps** a potvrďte nainstalovaného agenta.

8. V nabídce Start spusťte **RegEdit** jako správce.

9. Přejděte na **software\\\\\\computer HKEY_LOCAL_MACHINE FSLogix**.

10. Vytvořte klíč s názvem **Profily**.

11.  Vytvořte hodnotu s názvem **Povoleno** s **REG_DWORD** typem nastaveným na hodnotu dat **1**.

12. Vytvořte hodnotu s názvem **VHDLocations** s **víceřetězcovým** typem a nastavte její hodnotu dat na identifikátor URI pro sdílenou složku Soubory Azure NetApp.

13. Vytvořte hodnotu s názvem **DeleteLocalProfileWhenVHDShouldApply** s hodnotou DWORD 1, abyste před svým přihlášením zabránili problémům s existujícími místními profily.

     >[!WARNING]
     >Při vytváření hodnoty DeleteLocalProfileWhenVHDShouldApply buďte opatrní. Když systém Profily FSLogix zjistí, že uživatel by měl mít profil FSLogix, ale místní profil již existuje, kontejner profilu trvale odstraní místní profil. Uživatel bude poté přihlášen pomocí nového profilu FSLogix.

## <a name="assign-users-to-session-host"></a>Přiřazení uživatelů k hostiteli relace

1. Otevřete **PowerShell ISE** jako správce a přihlaste se k virtuální ploše Windows.

2. Spusťte následující rutiny:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Po zobrazení výzvy k zadání pověření zadejte pověření pro uživatele s rolemi Tvůrce klienta nebo Vlastník a přispěvatel RDS v tenantovi Virtuální plochy Windows.

4. Spusťte následující rutiny a přiřaďte uživatele ke skupině Vzdálená plocha:

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Ujistěte se, že uživatelé mají přístup ke sdílené složce Azure NetApp

1. Otevřete internetový prohlížeč <https://rdweb.wvd.microsoft.com/webclient/index.html>a přejděte na .

2. Přihlaste se pomocí přihlašovacích údajů uživatele přiřazených ke skupině Vzdálená plocha.

3. Po navázání uživatelské relace se přihlaste k portálu Azure pomocí účtu pro správu.

4. Otevřete **soubory Azure NetApp**, vyberte účet Soubory Azure NetApp a pak vyberte **Svazky**. Po otevření nabídky Objemy vyberte odpovídající hlasitost.

   ![Snímek obrazovky s účtem NetApp, který jste nastavili dříve na webu Azure Portal s vybraným tlačítkem Svazky.](media/netapp-account.png)

5. Přejděte na kartu **Přehled** a zkontrolujte, zda kontejner profilu FSLogix používá místo.

6. Připojte se přímo k libovolné části fondu hostitelů virtuálních počítačů pomocí vzdálené plochy a otevřete **Průzkumníka souborů.** Pak přejděte na **cestu připojení** (v následujícím \\ \\příkladu je cesta připojení anf-SMB-3863.gt1107.onmicrosoft.com\\anf-VOL).

   V této složce by měl být profil VHD (nebo VHDX) jako ten v následujícím příkladu.

   ![Snímek obrazovky s obsahem složky v cestě k připojení. Uvnitř je jeden soubor VHD s názvem "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Další kroky

Kontejnery profilů FSLogix můžete použít k nastavení sdílené složky profilu uživatele. Informace o tom, jak vytvořit sdílené složky profilu uživatele s novými kontejnery, najdete [v tématu Vytvoření kontejneru profilu pro fond hostitelů pomocí sdílené složky](create-host-pools-user-profile.md).
