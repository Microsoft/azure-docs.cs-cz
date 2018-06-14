---
title: Jak používat modul plug-in Azure podřízený s průběžnou integraci Hudsonem | Microsoft Docs
description: Popisuje, jak používat modul plug-in Azure podřízený s průběžnou integraci Hudsonem.
services: virtual-machines-linux
documentationcenter: ''
author: rmcmurray
manager: wpickett
editor: ''
ms.assetid: b2083d1c-4de8-4a19-a615-ccc9d9b6e1d9
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.openlocfilehash: c11b59f8ea432075b147a391de4b7bd3331e639e
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2018
ms.locfileid: "27704801"
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Jak používat modul plug-in Azure podřízený s Hudsonem průběžnou integraci
Modul plug-in pro Hudsonem Azure podřízený umožňuje zřídit podřízené uzly v Azure při spuštění distribuované sestavení.

## <a name="install-the-azure-slave-plug-in"></a>Instalace modulu plug-in Azure podřízený
1. Na řídicím panelu Hudsonem klikněte na tlačítko **spravovat Hudsonem**.
2. V **spravovat Hudsonem** klikněte na **Správa modulů plug-in**.
3. Klikněte **dostupné** kartě.
4. Klikněte na tlačítko **vyhledávání** a typ **Azure** k omezení seznamu k příslušné moduly plug-in.
   
    Pokud se přihlásíte vyhledejte v seznamu dostupných modulů plug-in, zjistí Azure podřízený modulu plug-in v části **správu clusteru a distribuovat sestavení** kapitoly **ostatní** kartě.
5. Zaškrtněte políčko **modul plug-in Azure podřízený**.
6. Klikněte na **Nainstalovat**.
7. Restartujte Hudsonem.

Teď, když je nainstalovaný, bude další kroky konfigurace modulu plug-in s profilem vašeho předplatného Azure a vytvořit šablonu, která se použije při vytváření virtuálního počítače pro podřízený uzel.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Modul plug-in Azure podřízený nakonfigurovat svůj profil předplatného
Odběru profil, který se také označuje jako nastavení publikování, je soubor XML, který obsahuje zabezpečené přihlašovací údaje a doplňující informace, které budete potřebovat pro práci s Azure ve vašem vývojovém prostředí. Pokud chcete konfigurovat modul plug-in Azure podřízený, potřebujete:

* Vaše id odběru
* Certifikát pro správu pro vaše předplatné

Ty lze najít ve vaší [odběru profil]. Dole je příklad profilu předplatného.

    <?xml version="1.0" encoding="utf-8"?>

        <PublishData>

          <PublishProfile SchemaVersion="2.0" PublishMethod="AzureServiceManagementAPI">

        <Subscription

              ServiceManagementUrl="https://management.core.windows.net"

              Id="<Subscription ID>"

              Name="Pay-As-You-Go"
            ManagementCertificate="<Management certificate value>" />

          </PublishProfile>

    </PublishData>

Až budete mít vaše předplatné profilu, postupujte podle těchto kroků nakonfigurujete Azure podřízený modulu plug-in.

1. Na řídicím panelu Hudsonem klikněte na tlačítko **spravovat Hudsonem**.
2. Klikněte na tlačítko **konfiguraci systému**.
3. Projděte dolů stránce Najít **cloudu** části.
4. Klikněte na tlačítko **přidat nové cloudové > Microsoft Azure**.
   
    ![Přidat nové cloudu][add new cloud]
   
    Zobrazí pole potřebujete-li zadat podrobnosti o vašem předplatném.
   
    ![Konfigurace profilu][configure profile]
5. Zkopírujte certifikát správy a id předplatného z vašeho profilu předplatného a vložte je do příslušných polí.
   
    Při kopírování id a správy certifikátu předplatného **nepodporují** zahrnout uvozovky, které uzavřete hodnoty.
6. Klikněte na **ověřte konfiguraci**.
7. Po konfiguraci je ověření bylo úspěšné, klikněte na tlačítko **Uložit**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Nastavení šablony virtuálního počítače pro podřízený Azure modulu plug-in
Šablonu virtuálního počítače definuje parametry, které modul plug-in použije k vytvoření podřízený uzel v Azure. V následujících krocích jsme budete vytvoření šablony pro virtuálního počítače s Ubuntu.

1. Na řídicím panelu Hudsonem klikněte na tlačítko **spravovat Hudsonem**.
2. Klikněte na **konfiguraci systému**.
3. Projděte dolů stránce Najít **cloudu** části.
4. V rámci **cloudu** část, vyhledejte **přidat šablonu virtuálního počítače Azure** a klikněte na tlačítko **přidat** tlačítko.
   
    ![Přidat šablonu virtuálního počítače.][add vm template]
5. Zadejte název cloudové služby v **název** pole. Pokud název, který zadáte odkazuje na existující službu cloud, virtuální počítač se zřídí v této službě. Jinak Azure vytvoří novou.
6. V **popis** pole, zadejte text, který popisuje šablonu, kterou vytváříte. Tyto informace je pouze pro účely písemné a nepoužívá se v zřizování virtuálního počítače.
7. V **popisky** zadejte **linux**. Tento popisek slouží k identifikaci šablonu, kterou vytváříte a následně slouží k odkazování šablonu při vytváření úlohy Hudsonem.
8. Vyberte oblast, kde bude vytvořen virtuální počítač.
9. Vyberte odpovídající velikost virtuálního počítače.
10. Zadejte účet úložiště, kde bude vytvořen virtuální počítač. Ujistěte se, že je ve stejné oblasti jako cloudová služba, kterou budete používat. Pokud chcete vytvořit nové úložiště, můžete toto pole zůstat prázdné.
11. Doba uchování určuje počet minut, než Hudsonem odstraní nečinnosti podřízený. Nechte na výchozí hodnotu 60.
12. V **využití**, vyberte vhodné podmínky, když se použije tento podřízený uzel. Nyní, vyberte **využívají tento uzel co nejvíce**.
    
     Formulář v tomto okamžiku by vypadat poněkud podobná této:
    
     ![Konfigurace šablony][template config]
13. V **řady bitovou kopii nebo Id** budete muset určit, jaké bitové kopie systému bude nainstalována na váš počítač. Můžete vybrat ze seznamu rodin bitové kopie, nebo zadejte vlastní image.
    
     Pokud chcete vybrat ze seznamu rodiny bitové kopie, zadejte první znak (malá a velká písmena) název rodiny bitové kopie. Například zadáním **U** zobrazíte seznam rodiny Ubuntu Server. Jakmile vyberete ze seznamu, volaných používat nejnovější verzi této bitové kopie systému z této rodiny při zřízení virtuálního počítače.
    
     ![Rodiny seznamu operačního systému][OS family list]
    
     Pokud máte vlastní image, kterou chcete použít místo toho, zadejte název této vlastní bitové kopie. V seznamu nejsou zobrazeny názvy vlastní image, musíte zkontrolovat, zda je správně zadán název.    
    
     V tomto kurzu zadejte **U** zobrazte seznam Image Ubuntu a vyberte **Ubuntu Server 14.04 LTS**.
14. Pro **spusťte metoda**, vyberte **SSH**.
15. Zkopírujte následující skript a vložte **Init skriptu** pole.
    
         # Install Java
    
         sudo apt-get -y update
    
         sudo apt-get install -y openjdk-7-jdk
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y openjdk-7-jdk
    
         # Install git
    
         sudo apt-get install -y git
    
         #Install ant
    
         sudo apt-get install -y ant
    
         sudo apt-get -y update --fix-missing
    
         sudo apt-get install -y ant
    
     **Init skriptu** bude proveden po vytvoření virtuálního počítače. V tomto příkladu skript nainstaluje ant, Java a git.
16. V **uživatelské jméno** a **heslo** pole, zadejte svoje upřednostňované hodnoty pro účet správce, který se vytvoří na vašem virtuálním počítači.
17. Klikněte na **ověřte šablony** ke kontrole, jestli jsou parametry jste zadali platný.
18. Klikněte na **Uložit**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Vytvořit úlohu Hudsonem, který běží na uzlu podřízený v Azure
V této části budete vytváření Hudsonem úlohu, která se spustí na podřízený uzel v Azure.

1. Na řídicím panelu Hudsonem klikněte na tlačítko **nová úloha**.
2. Zadejte název pro úlohu, kterou vytváříte.
3. Typ úlohy, vyberte **sestavení úloha softwaru bez stylu**.
4. Klikněte na **OK**.
5. Na stránce konfigurace úlohy, vyberte **omezit, kde můžete spustit tento projekt**.
6. Vyberte **uzlu a popisek nabídky** a vyberte **linux** (jsme zadali tento popisek, při vytváření šablony virtuálního počítače v předchozí části).
7. V **sestavení** klikněte na tlačítko **přidat krok sestavení** a vyberte **spustit prostředí**.
8. Upravte následující skript, nahraďte **{název účtu github}**, **{název projektu}**, a **{adresáři projektu}** s příslušným hodnoty a vložit upravená skript v části textu, který se zobrazí.
   
        # Clone from git repo
   
        currentDir="$PWD"
   
        if [ -e {your project directory} ]; then
   
              cd {your project directory}
   
              git pull origin master
   
        else
   
              git clone https://github.com/{your github account name}/{your project name}.git
   
        fi
   
        # change directory to project
   
        cd $currentDir/{your project directory}
   
        #Execute build task
   
        ant
9. Klikněte na **Uložit**.
10. Na řídicím panelu Hudsonem najít úlohu, kterou jste právě vytvořili a klikněte na **naplánovat sestavení** ikonu.

Hudsonem se pak vytvořit podřízený uzel pomocí šablony vytvořené v předchozí části a spustit skript, který jste zadali v kroku sestavení pro tuto úlohu.

## <a name="next-steps"></a>Další kroky
Další informace o používání Javy v Azure najdete na webu [Středisko pro vývojáře Java].

<!-- URL List -->

[Středisko pro vývojáře Java]: https://azure.microsoft.com/develop/java/
[odběru profil]: http://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

