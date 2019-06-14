---
title: Jak používat podřízený Plugin Azure s Hudson Continuous Integration | Dokumentace Microsoftu
description: Popisuje, jak používat podřízený Plugin Azure s Hudson Continuous Integration.
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
ms.openlocfilehash: ef24e356c9ac8424fc519a3b16af5d37a20e706f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60444145"
---
# <a name="how-to-use-the-azure-slave-plug-in-with-hudson-continuous-integration"></a>Jak používat podřízený Plugin Azure s Hudson Continuous Integration
Modul plug-in pro Hudson Azure slave umožňuje zřizovat podřízených uzlů v Azure, při spouštění distribuovaných sestavení.

## <a name="install-the-azure-slave-plug-in"></a>Instalace modulu plug-in Azure Slave
1. Na řídicím panelu Hudson, klikněte na tlačítko **spravovat Hudson**.
2. V **spravovat Hudson** stránky, klikněte na **Správa modulů plug-in**.
3. Klikněte na tlačítko **dostupné** kartu.
4. Klikněte na tlačítko **hledání** a typ **Azure** k omezení seznamu do příslušných modulů plug-in.
   
    Pokud se rozhodnete procházejte seznam dostupných modulů plug-in, najdou podřízený server Azure v rámci modulu plug-in **správu clusteru a distribuovat sestavení** tématu **ostatní** kartu.
5. Zaškrtněte políčko pro **modul plug-in Azure Slave**.
6. Klikněte na **Nainstalovat**.
7. Restart Hudson.

Teď, když je nainstalovaný modul plug-in, bude další kroky konfigurace modulu plug-in pomocí profilu předplatného Azure a chcete-li vytvořit šablonu, která se použije při vytváření virtuálního počítače pro podřízený uzel.

## <a name="configure-the-azure-slave-plug-in-with-your-subscription-profile"></a>Konfigurace vašeho profilu předplatného Azure podřízený Plugin
Předplatné profil, který se také označuje jako nastavení publikování, je soubor XML, který obsahuje zabezpečené přihlašovací údaje a některé další informace, které budete potřebovat pro práci s Azure ve vašem vývojovém prostředí. Pokud chcete nakonfigurovat podřízený Plugin Azure, budete potřebovat:

* Id vašeho předplatného
* Certifikát pro správu pro vaše předplatné

Ty lze najít ve vaší [profilu předplatného]. Níže je příklad profilu předplatného.

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

Jakmile budete mít vaše předplatné profilu, postupujte podle těchto kroků a nakonfigurujte podřízený Plugin Azure.

1. Na řídicím panelu Hudson, klikněte na tlačítko **spravovat Hudson**.
2. Klikněte na tlačítko **konfiguraci systému**.
3. Přejděte dolů na stránce Najít **cloudu** oddílu.
4. Klikněte na tlačítko **přidat nový cloud > Microsoft Azure**.
   
    ![Přidat nový cloud][add new cloud]
   
    Tím se zobrazí pole ve kterém budete muset zadat podrobnosti o předplatném.
   
    ![Konfigurace profilu][configure profile]
5. Zkopírujte certifikát správy a id předplatného z vašeho profilu předplatného a vložte je do příslušných polí.
   
    Při kopírování id a správy certifikátu předplatného **nejsou** obsahovat uvozovky, které hodnoty.
6. Klikněte na **ověřte konfiguraci**.
7. Po konfiguraci jejího úspěšného ověření, klikněte na tlačítko **Uložit**.

## <a name="set-up-a-virtual-machine-template-for-the-azure-slave-plug-in"></a>Nastavení šablony virtuálního počítače pro podřízený server Azure modulu plug-in
Šablonu virtuálního počítače definuje parametry, které modul plug-in použije k vytvoření uzlu podřízený server v Azure. V následujících krocích jsme vám vytvoření šablony pro virtuální počítač s Ubuntu.

1. Na řídicím panelu Hudson, klikněte na tlačítko **spravovat Hudson**.
2. Klikněte na **konfiguraci systému**.
3. Přejděte dolů na stránce Najít **cloudu** oddílu.
4. V rámci **cloudu** části, Najít **přidat šablonu virtuálního počítače Azure** a klikněte na tlačítko **přidat** tlačítko.
   
    ![Přidat šablonu virtuálního počítače][add vm template]
5. Zadejte název cloudové služby v **název** pole. Pokud zadáte název odkazuje na existující cloudové služby, virtuální počítač se zřídí v této službě. V opačném případě bude Azure vytvořte nový.
6. V **popis** zadejte text, který popisuje šablonu, kterou vytváříte. Tyto informace slouží jenom k dokumentární a nepoužívá ve zřizování virtuálního počítače.
7. V **popisky** zadejte **linux**. Tento popisek se používá k identifikaci šablona, kterou vytváříte a následně slouží jako odkaz šablonu při vytváření úlohy Hudson.
8. Vyberte oblast, ve kterém se vytvoří virtuální počítač.
9. Vyberte odpovídající velikost virtuálního počítače.
10. Zadejte účet úložiště, ve kterém se vytvoří virtuální počítač. Ujistěte se, že je ve stejné oblasti jako cloudové služby, které budete používat. Pokud chcete vytvořit nové úložiště, můžete ponechat toto pole prázdné.
11. Doba uchování určuje počet minut, než se odstraní Hudson nečinnosti podřízený server. Nechte to na výchozí hodnotu 60.
12. V **využití**, vyberte příslušné podmínky, když se použije tento podřízený uzel. Teď vyberte **využívat co nejlépe tento uzel**.
    
     V tomto okamžiku by vypadalo formuláře poněkud podobný tomuto:
    
     ![Konfigurace šablony][template config]
13. V **řadu Image nebo Id** budete muset určit, jaké image systému budou nainstalovány na vašem virtuálním počítači. Můžete vybrat ze seznamu obrázků rodin, nebo zadejte vlastní image.
    
     Pokud chcete vybrat ze seznamu obrázků rodiny, zadejte první znak (malá a velká písmena) název rodiny bitové kopie. Například zadáním **U** se otevře seznam skupin Ubuntu Server. Jakmile vyberete ze seznamu, Jenkins používat nejnovější verzi této bitové kopie systému v dané řadě při zřizování virtuálního počítače.
    
     ![Operační systém řady seznamu][OS family list]
    
     Pokud máte vlastní image, kterou chcete použít místo toho zadejte název této vlastní image. Název vlastní image se nezobrazují v seznamu, proto ověřte, zda je správně zadán název.    
    
     Pro účely tohoto kurzu zadejte **U** zobrazte seznam imagemi Ubuntu a vyberte **Ubuntu Server 14.04 LTS**.
14. Pro **metoda spuštění**vyberte **SSH**.
15. Zkopírujte níže uvedený skript a vložte **Init skript** pole.
    
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
    
     **Init skript** se spustí po vytvoření virtuálního počítače. Skript v tomto příkladu nainstaluje ant, Java a git.
16. V **uživatelské jméno** a **heslo** pole, zadejte upřednostňovanou hodnot pro účet správce, který se vytvoří na vašem virtuálním počítači.
17. Klikněte na **ověření šablony** ke kontrole, jestli jsou parametry, které jste zadali platné.
18. Klikněte na **Uložit**.

## <a name="create-a-hudson-job-that-runs-on-a-slave-node-on-azure"></a>Vytvoření úlohy Hudson, který běží na uzlu podřízený server v Azure
V této části vytvoříte Hudson úlohu, která se spustí na podřízený uzel v Azure.

1. Na řídicím panelu Hudson, klikněte na tlačítko **nová úloha**.
2. Zadejte název pro úlohu, kterou vytváříte.
3. Typ úlohy, vyberte **sestavení softwaru volný styl projektu**.
4. Klikněte na **OK**.
5. Na stránce konfigurace úlohy, vyberte **omezit, kde lze tento projekt spustit**.
6. Vyberte **uzlu a popisek nabídky** a vyberte **linux** (jsme zadali tento popisek, při vytváření šablony virtuálního počítače v předchozí části).
7. V **sestavení** klikněte na tlačítko **přidat krok sestavení** a vyberte **spustit prostředí**.
8. Upravte následující skript, nahrazení **{název účtu github}** , **{název vašeho projektu}** , a **{adresáři projektu}** s odpovídající hodnoty a vložit upravený skript v části textu, který se zobrazí.
   
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
10. Na řídicím panelu Hudson najít úlohu, jste právě vytvořili a klikněte na **plánovat sestavení** ikonu.

Hudson se pak vytvořit podřízený uzel pomocí šablony vytvořené v předchozí části a spusťte tento skript, který jste zadali v kroku sestavení pro tuto úlohu.

## <a name="next-steps"></a>Další kroky
Další informace o používání Javy v Azure najdete na webu [Středisko pro vývojáře Java].

<!-- URL List -->

[Středisko pro vývojáře Java]: https://azure.microsoft.com/develop/java/
[profilu předplatného]: https://go.microsoft.com/fwlink/?LinkID=396395

<!-- IMG List -->

[add new cloud]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addcloud.png
[configure profile]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-configureprofile.png
[add vm template]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-addnewvmtemplate.png
[template config]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-setup-templateconfig1-withdata.png
[OS family list]: ./media/virtual-machines-azure-slave-plugin-for-hudson/hudson-oslist.png

