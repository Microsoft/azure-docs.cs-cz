---
title: Použití externího souborového úložiště v testovacích službách | Microsoft Docs
description: Naučte se, jak nastavit testovací prostředí, které využívá úložiště externích souborů v testovacích službách.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 888e04db76567051f8c5eae7cf94c77e684cb146
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111564"
---
# <a name="using-external-file-storage-in-lab-services"></a>Použití externího souborového úložiště v testovacích službách

Tento článek se zabývá některými možnostmi pro ukládání externích souborů při použití Azure Lab Services.  [Soubory Azure](https://azure.microsoft.com/services/storage/files/) nabízí plně spravované sdílené složky v cloudu, které jsou [přístupné přes SMB 2,1 a SMB 3,0.](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)  Sdílenou složku služby soubory Azure je možné připojit buď veřejně, nebo soukromě v rámci virtuální sítě.  Dá se taky nakonfigurovat tak, aby se k připojení ke sdílené složce používaly přihlašovací údaje služby AD studenta.  Použití Azure NetApp Files se svazky NFS pro počítače se systémem Linux je další možností pro ukládání externích souborů s Azure Lab Services.  

## <a name="deciding-which-solution-to-use"></a>Rozhodnutí o tom, které řešení se má použít

Každé řešení má různé požadavky a možnosti.  V tabulce níže jsou uvedeny důležité body, které je potřeba zvážit pro každé řešení.  

|     Řešení    |    Důležité informace    |
| -------------- | ------------------------ |
| [Sdílená složka služby soubory Azure s veřejným koncovým bodem](#azure-files-share) | <ul><li>Všichni uživatelé mají přístup pro čtení i zápis.</li><li>Nevyžaduje se žádný partnerský vztah virtuální sítě.</li><li>Přístup ke všem virtuálním počítačům, nejen pouze k testovacím virtuálním počítačům.</li><li>Pokud používáte Linux, studenti budou mít přístup k klíči účtu úložiště.</li></ul> |
| [Sdílená složka Azure Files s privátním koncovým bodem](#azure-files-share) | <ul><li>Všichni uživatelé mají přístup pro čtení i zápis.</li><li>Vyžaduje se partnerský vztah virtuální sítě.</li><li>Dostupné jenom pro virtuální počítače ve stejné síti (nebo v partnerské síti) jako účet úložiště.</li><li>Pokud používáte Linux, studenti budou mít přístup k klíči účtu úložiště.</li></ul> |
| [Soubory Azure s ověřováním na základě identity](#azure-files-with-identity-base-authorization) | <ul><li>Pro složku nebo soubor lze nastavit buď oprávnění ke čtení, nebo čtení i zápis.</li><li>Vyžaduje se partnerský vztah virtuální sítě.</li><li>Účet úložiště musí být připojený ke službě Active Directory.</li><li>Virtuální počítače testovacího prostředí musí být připojené k doméně.</li><li>Klíč účtu úložiště se nepoužívá pro studenty, kteří se k této sdílené složce připojí.</li></ul> |
| [Soubory NetApp se svazky NFS](#netapp-files-with-nfs-volumes) | <ul><li>Pro svazky lze nastavit buď přístup pro čtení, nebo pro čtení i zápis.</li><li>Oprávnění se nastavují pomocí IP adresy virtuálního počítače studenta.</li><li>Vyžaduje se partnerský vztah virtuální sítě.</li><li>Může být nutné zaregistrovat se pro používání služby soubory NetApp.</li><li>Pouze Linux.</li></ul>

Náklady na používání externího úložiště nejsou zahrnuté do nákladů na používání Azure Lab Services.  Další informace o cenách najdete v tématu ceny za [Azure Files](https://azure.microsoft.com/pricing/details/storage/files/) a [Azure NetApp Files ceny](https://azure.microsoft.com/pricing/details/netapp/).

## <a name="azure-files-share"></a>Sdílená složka Azure Files

Ke sdíleným složkám Azure se dostanete pomocí veřejného nebo privátního koncového bodu.  Sdílené složky Azure jsou připojené pomocí klíče účtu úložiště jako hesla.  S tímto přístupem mají všichni uživatelé ke sdílené složce přístup pro čtení i zápis.

Pokud používáte veřejný koncový bod pro sdílenou složku služby soubory Azure, je důležité si uvědomit:

- Virtuální síť pro účet úložiště nemusí být v partnerském vztahu k účtu testovacího prostředí.  Sdílenou složku můžete vytvořit kdykoli předtím, než se publikuje virtuální počítač šablony.
- Ke sdílené složce se dá dostat z libovolného počítače, pokud má uživatel klíč účtu úložiště.  
- Studenti pro Linux uvidí klíč účtu úložiště.  Přihlašovací údaje pro připojení sdílené složky služby soubory Azure se ukládají do `{file-share-name}.cred` virtuálních počítačů se systémem Linux a čitelné pomocí sudo.  Vzhledem k tomu, že studenti mají ve výchozím nastavení na virtuálních počítačích Azure Lab Service přístup sudo, můžou si přečíst klíč účtu úložiště.  Pokud je koncový bod účtu úložiště veřejný, můžou studenti získat přístup ke sdílené složce mimo svůj virtuální počítač studenta.  Zvažte otočení klíče účtu úložiště po ukončení třídy a používání privátních sdílených složek.

Pokud používáte privátní koncový bod ke sdílené složce služby soubory Azure, je důležité si uvědomit:

- Přístup je omezený na provoz pocházející z privátní sítě a není přístupný prostřednictvím veřejného Internetu.  Pouze virtuální počítače v privátní virtuální síti, virtuální počítače v síti připojené k privátní virtuální síti nebo počítačům připojeným k síti VPN pro privátní síť mají přístup ke sdílené složce.  
- Studenti pro Linux uvidí klíč účtu úložiště.  Přihlašovací údaje pro připojení sdílené složky služby soubory Azure se ukládají do `{file-share-name}.cred` virtuálních počítačů se systémem Linux a čitelné pomocí sudo.  Vzhledem k tomu, že studenti mají ve výchozím nastavení na virtuálních počítačích Azure Lab Service přístup sudo, můžou si přečíst klíč účtu úložiště.  Po ukončení třídy zvažte otočení klíče účtu úložiště.
- Tento přístup vyžaduje, aby se virtuální síť sdílené složky Nasdílela k účtu testovacího prostředí.  **Před** vytvořením testovacího prostředí musí být virtuální síť pro účet Azure Storage partnerským vztahem k virtuální síti pro účet testovacího prostředí.

> [!NOTE]
> Sdílené složky, které jsou větší než 5 TB, jsou k dispozici pouze pro [účty [místně redundantní úložiště (LRS)]](/azure/storage/files/storage-files-how-to-create-large-file-share#restrictions).

Pomocí těchto kroků můžete vytvořit virtuální počítač připojený ke sdílené složce Azure.

1. Vytvořte [účet Azure Storage](/azure/storage/files/storage-how-to-create-file-share). Na stránce způsob připojení vyberte veřejný koncový bod nebo soukromý koncový bod.
2. Pokud používáte, vytvořte [privátní koncový bod](/azure/private-link/create-private-endpoint-storage-portal) , aby byly sdílené složky dostupné z této virtuální sítě.  Vytvořte [privátní ZÓNU DNS](/azure/dns/private-dns-privatednszone) nebo použijte existující. Zóny privátního Azure DNS poskytují překlad adres IP v rámci virtuální sítě.
3. Vytvořte [sdílenou složku Azure](/azure/storage/files/storage-how-to-create-file-share). Sdílená složka je dosažitelná názvem veřejného hostitele účtu úložiště.
4. Připojte sdílenou složku Azure na virtuálním počítači šablony:
    - [Systému](/azure/storage/files/storage-how-to-use-files-windows)
    - [[Linux]](/azure/storage/files/storage-how-to-use-files-linux).  V tématu [použití souborů Azure se systémem Linux](#using-azure-files-with-linux) se vyhnete problémům s připojením k virtuálním počítačům studenta.
5. [Publikujte](how-to-create-manage-template.md#publish-the-template-vm) virtuální počítač šablony.

> [!IMPORTANT]
> Ujistěte se, že brána firewall neblokuje odchozí připojení SMB přes port 445. Ve výchozím nastavení je protokol SMB povolený pro virtuální počítače Azure.

### <a name="using-azure-files-with-linux"></a>Používání služby soubory Azure se systémem Linux

Pokud použijete _Výchozí pokyny_ k připojení sdílené složky Azure, bude se tato sdílená složka po publikování šablony zdát na virtuálních počítačích studenta.  Níže je upraven skript, který tento problém řeší.  

```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Pokud je virtuální počítač šablony již publikovaný, který do adresáře připojí sdílenou složku Azure `/mnt` , může student provést jednu z následujících akcí.

- Přesuňte instrukci pro připojení `/mnt` k horní části `/etc/fstab` souboru.  
- Upravte instrukci pro připojení `/mnt/{file-share-name}` k jinému adresáři jako `/prm-mnt/{file-share-name}` .

Studenti by měli spustit `mount -a` pro opětovné připojení adresářů.

Obecnější informace o používání sdílených složek se systémem Linux najdete v tématu [použití souborů Azure se systémem Linux](/azure/storage/files/storage-how-to-use-files-linux).

## <a name="azure-files-with-identity-base-authorization"></a>Soubory Azure s ověřováním na základě identity

Ke sdíleným složkám souborů Azure se dá také přístup pomocí ověřování AD, pokud

1. Virtuální počítač studenta je připojený k doméně.
2. [Na účtu Azure Storage](/azure/storage/files/storage-files-active-directory-overview) , který hostuje sdílenou složku, je povoleno ověřování AD.  

Síťová jednotka je připojena k virtuálnímu počítači pomocí identity uživatele, nikoli klíče k účtu úložiště.  Přístup k účtu úložiště může používat veřejné nebo soukromé koncové body.

Pojďme si projít některé důležité body tohoto přístupu.

- Oprávnění lze nastavit na úrovni adresáře nebo souboru.
- Přihlašovací údaje aktuálního uživatele se používají k ověření sdílené složky.

Pokud používáte veřejný koncový bod pro sdílenou složku služby soubory Azure, je důležité si uvědomit:

- Virtuální síť pro účet úložiště nemá na účet testovacího prostředí vztah.  Sdílenou složku můžete vytvořit kdykoli předtím, než se publikuje virtuální počítač šablony.

Pokud používáte privátní koncový bod ke sdílené složce služby soubory Azure, je důležité si uvědomit:

- Přístup je omezený na provoz pocházející z privátní sítě a není přístupný prostřednictvím veřejného Internetu.  Pouze virtuální počítače v privátní virtuální síti, virtuální počítače v síti připojené k privátní virtuální síti nebo počítačům připojeným k síti VPN pro privátní síť mají přístup ke sdílené složce.  
- Tento přístup vyžaduje, aby se virtuální síť sdílené složky Nasdílela k účtu testovacího prostředí.  **Před** vytvořením testovacího prostředí musí být virtuální síť pro účet Azure Storage partnerským vztahem k virtuální síti pro účet testovacího prostředí.

Pomocí následujících kroků vytvořte sdílenou složku Azure Files s povoleným ověřováním AD a připojte se k virtuálním počítačům testovacího prostředí v doméně.

1. Vytvořte [účet Azure Storage](/azure/storage/files/storage-how-to-create-file-share).
2. Pokud používáte, vytvořte [privátní koncový bod](/azure/private-link/create-private-endpoint-storage-portal) , aby byly sdílené složky dostupné z této virtuální sítě.  Vytvořte [privátní ZÓNU DNS](/azure/dns/private-dns-privatednszone) nebo použijte existující. Zóny privátního Azure DNS poskytují překlad adres IP v rámci virtuální sítě.
3. Vytvořte [sdílenou složku Azure](/azure/storage/files/storage-how-to-create-file-share).
4. Pokud chcete povolit autorizaci založenou na identitě, postupujte podle těchto kroků.  Pokud používáte Prem AD, který je synchronizovaný s Azure AD, postupujte podle kroků pro [místní Active Directory Domain Services ověřování pomocí protokolu SMB pro sdílené složky Azure](/azure/storage/files/storage-files-identity-auth-active-directory-enable).  Pokud používáte jenom Azure AD, postupujte podle kroků a [povolte Azure Active Directory Domain Services ověřování u souborů Azure](/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable).
    >[!IMPORTANT]
    >Promluvte si tým, který spravuje vaši reklamu a ověřte, jestli jsou splněné všechny požadavky uvedené v pokynech.
5. Přiřaďte role oprávnění ke sdílení SMB v Azure.  Podrobnosti o oprávněních, která jsou udělena jednotlivým rolím, najdete v tématu [oprávnění na úrovni sdílené složky](/azure/storage/files/storage-files-identity-ad-ds-assign-permissions).
    1. Roli "soubor úložiště dat sdílené složky SMB se zvýšenými oprávněními" musí být přiřazená osobě nebo skupině, která nastaví oprávnění pro obsah sdílené složky.
    2. K studentům, kteří potřebují přidávat nebo upravovat soubory ve sdílené složce, by měla být přiřazena role Přispěvatel sdílené složky SMB pro data souborů úložiště.
    3. Role čtečky sdílené složky SMB pro data souborů by se měla přiřadit studentům, kteří budou muset číst jenom soubory ze sdílené složky.
6. Nastavte pro sdílenou složku oprávnění na úrovni adresářů a souborů.  Oprávnění musí být nastavena z počítače připojeného k doméně, který má síťový přístup ke sdílené složce.  **Pokud chcete upravit oprávnění na úrovni adresářů a souborů, připojte sdílenou složku pomocí klíče úložiště, nikoli přihlašovacích údajů k AAD.**  [Nastavení ACL](/powershell/module/microsoft.powershell.security/set-acl) Příkazy prostředí PowerShell nebo [Icacls](/windows-server/administration/windows-commands/icacls) jsou doporučenými nástroji, které je vhodné použít při přiřazování oprávnění.
7. Navázání [partnerského vztahu virtuální sítě](how-to-connect-peer-virtual-network.md) pro účet úložiště k účtu testovacího prostředí.
8. [Vytvořte prostředí učebny](how-to-manage-classroom-labs.md).
9. Uložte skript na virtuální počítač šablony, který můžou studenti spustit, aby se připojili k síťové jednotce.  Postup získání ukázkového skriptu:
    1. Otevřete účet úložiště na webu Azure Portal.
    1. V nabídce **Souborová služba** vyberte **sdílené složky** .
    1. Najděte sdílenou složku, ke které se chcete připojit, vyberte tlačítko se třemi tečkami úplně vpravo a zvolte **připojit**.
    1. V části **připojení** se otevře s pokyny pro Windows, Linux a MacOS.  Pokud používáte systém Windows, nastavte **metodu ověřování** na **Active Directory**.
    1. Zkopírujte kód v příkladu a uložte ho na počítači šablony do `.ps1` souboru pro Windows nebo `.sh` soubor pro Linux.
10. Na počítači šablony Stáhněte a spusťte skript pro [připojení počítačů studentů k doméně](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage).  `Join-AzLabADTemplate`Skript automaticky [PUBLIKUJE virtuální počítač šablony](how-to-create-manage-template.md#publish-the-template-vm) .  
    > [!NOTE]
    > Počítač šablony nebude připojen k doméně. Instruktoři by měli pro sebe přiřadit publikovaný virtuální počítač studenta, aby mohli zobrazit soubory ve sdílené složce.
11. Studenti, kteří používají Windows, se můžou ke sdílené složce Azure Files připojit pomocí [Průzkumníka souborů](/azure/storage/files/storage-how-to-use-files-windows) s přihlašovacími údaji, jakmile se dokončí cesta ke sdílené složce souborů.  Případně můžou studenti spustit skript vytvořený výše, aby se připojil k síťové jednotce.  Pro studenty, kteří používají Linux, spusťte skript vytvořený výše.

## <a name="netapp-files-with-nfs-volumes"></a>Soubory NetApp se svazky NFS

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) je vysoce výkonná služba úložiště monitorovaných souborů na podnikové úrovni.

- Zásady přístupu se dají nastavit na jednotlivých svazcích.
- Zásady oprávnění jsou založené na IP adresách pro každý svazek.
- Pokud student potřebuje svůj vlastní svazek, ke kterému nemají přístup jiní studenti, musí se po publikování testovacího prostředí přiřadit zásady oprávnění.
- V kontextu Azure Lab Services se podporují jenom počítače se systémem Linux.
- Virtuální síť pro fond kapacit Azure NetApp Files musí být partnerským vztahem k virtuální síti pro účet testovacího prostředí **před** vytvořením testovacího prostředí.

Postupujte podle následujících kroků a použijte Azure NetApp Files sdílenou složku v Azure Lab Services.

1. Připojte se [Azure NetApp Files](https://aka.ms/azurenetappfiles), pokud je to potřeba.
2. Pokud chcete vytvořit fond kapacity souborů NetApp a svazky NFS, přečtěte si téma [nastavení Azure NetApp Files a svazku NFS](/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes).  Informace o úrovních služeb najdete v tématu [úrovně služeb pro Azure NetApp Files](/azure/azure-netapp-files/azure-netapp-files-service-levels).
3. [Partnerský vztah virtuální sítě](how-to-connect-peer-virtual-network.md) pro fond kapacity souborů NetApp k účtu testovacího prostředí.
4. [Vytvořte prostředí učebny](how-to-manage-classroom-labs.md).
5. Na virtuálním počítači šablony nainstalujte potřebné komponenty pro použití sdílených složek NFS.
    - Ubuntu

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. Na virtuálním počítači šablony uložte skript níže, `mount_fileshare.sh` aby bylo možné [připojit sdílenou složku souborů NetApp](/azure/azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines).  Přiřaďte `capacity_pool_ipaddress` proměnné IP adresu cíle připojení pro fond kapacit.  Získejte pokyny pro připojení ke svazku, aby se vyhledala příslušná hodnota.  Skript očekává cestu nebo název svazku souborů NetApp.  Nezapomeňte spustit `chmod u+x mount_fileshare.sh` , abyste zajistili, že skript může být proveden uživateli.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Pokud všichni studenti sdílejí přístup ke stejnému svazku NetApp souborů, `mount_fileshare.sh` můžete skript spustit na počítači šablony před publikováním.  Pokud studenté získají vlastní svazek, uložte skript, který má spustit později.
8. [Publikujte](how-to-create-manage-template.md#publish-the-template-vm) virtuální počítač šablony.
9. [Nakonfigurujte zásady](/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) pro sdílení souborů.  Zásady exportu můžou umožňovat, aby jeden virtuální počítač nebo více virtuálních počítačů měl přístup ke svazku.  Lze udělit pouze čtení nebo přístup pro čtení a zápis.
10. Studenti musí spustit svůj virtuální počítač a spustit skript pro připojení sdílené složky.  Stačí spustit skript jenom jednou.  Příkaz bude vypadat jako `./mount_fileshare.sh myvolumename` .

## <a name="next-steps"></a>Další kroky

Další kroky jsou běžné pro nastavení testovacího prostředí.

- [Vytvoření a Správa šablony](how-to-create-manage-template.md)
- [Přidávání uživatelů](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Nastavit kvótu](how-to-configure-student-usage.md#set-quotas-for-users)
- [Nastavit plán](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Odkazy na registraci e-mailu studentům](how-to-configure-student-usage.md#send-invitations-to-users)