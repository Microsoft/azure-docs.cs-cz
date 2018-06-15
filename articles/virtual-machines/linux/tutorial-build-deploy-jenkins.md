---
title: Kurz – CI/CD z Jenkins na virtuální počítače Azure pomocí služby Team Services | Microsoft Docs
description: V tomto kurzu se naučíte, jak nastavit kontinuální integraci (CI) a průběžné nasazování (CD) aplikace Node.js pomocí Jenkins na virtuální počítače Azure ze správy vydaných verzí v produktech Visual Studio Team Services nebo Microsoft Team Foundation Server.
author: ahomer
manager: douge
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/19/2017
ms.author: ahomer
ms.custom: mvc
ms.openlocfilehash: fc301edf13f8e6874f0b77440e2b0dc01b2a55fc
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32189927"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-visual-studio-team-services"></a>Kurz: Nasazení aplikace na linuxové virtuální počítače v Azure pomocí Jenkins a Visual Studio Team Services

Kontinuální integrace (CI) a průběžné nasazování (CD) tvoří kanál, s jehož pomocí můžete sestavovat, vydávat a nasazovat kód. Visual Studio Team Services poskytuje kompletní plnohodnotnou sadu nástrojů pro automatizaci CI/CD umožňující nasazení do Azure. Jenkins je populární serverový nástroj CI/CD od jiného výrobce, který rovněž nabízí automatizaci CI/CD. Kombinací produktů Team Services a Jenkins můžete přizpůsobit způsob, jakým dodáváte cloudovou aplikaci nebo službu.

V tomto kurzu použijete Jenkins k sestavení webové aplikace Node.js. Pak ji přes Team Services nebo Team Foundation Server nasadíte do [skupiny nasazení](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/), která obsahuje linuxové virtuální počítače. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Získání ukázkové aplikace
> * Konfigurace modulů plug-in Jenkins
> * Konfigurace projektu Jenkins Freestyle pro Node.js
> * Konfigurace Jenkins pro integraci se službou Team Services
> * Vytvoření koncového bodu služby Jenkins
> * Vytvoření skupiny nasazení pro virtuální počítače Azure
> * Vytvoření definice verze Team Services
> * Ruční nasazení nebo nasazení aktivované přes CI

## <a name="before-you-begin"></a>Než začnete

* Potřebujete přístup k serveru Jenkins. Pokud jste ještě nevytvořili server Jenkins, přečtěte si článek o [vytvoření hlavní databáze Jenkins ve virtuálním počítači Azure](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Přihlaste se ke svému účtu služby Team Services (**https://{váš-účet}.visualstudio.com**). 
  Můžete získat [bezplatný účet služby Team Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Další informace najdete v tématu o [připojení ke službě Team Services](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services).

*  Potřebujete linuxový virtuální počítač jako cíl nasazení.  Další informace najdete v článku o [vytvoření a správě linuxových virtuálních počítačů pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Otevřete příchozí port 80 pro virtuální počítač. Další informace najdete v článku o [vytvoření skupin zabezpečení sítě pomocí Azure Portalu](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

## <a name="get-the-sample-app"></a>Získání ukázkové aplikace

K nasazení potřebujete nějakou aplikaci uloženou v úložišti Gitu.
V tomto kurzu doporučujeme použít [tuto ukázkovou aplikaci dostupnou na GitHubu](https://github.com/azooinmyluggage/fabrikam-node). Tento kurz obsahuje ukázkový skript, který se používá k instalaci Node.js a aplikace. Pokud chcete pracovat se svým vlastním úložištěm, měli byste si nakonfigurovat podobnou ukázku.

Vytvořte fork této aplikace a poznačte si umístění (adresu URL), které použijete později v tomto kurzu. Další informace najdete v článku o [vytvoření forku úložiště](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> Tato aplikace byla vytvořena přes [Yeoman](http://yeoman.io/learning/index.html). Používá Express, Bower a Grunt. Jako závislosti také obsahuje několik balíčků npm.
> Součástí této ukázky je také skript, který nainstaluje Nginx a nasadí tuto aplikaci. Spouští se na virtuálních počítačích. Tento skript konkrétně:
> 1. Nainstaluje Node, Nginx a PM2
> 2. Nakonfiguruje Nginx a PM2
> 3. Spustí aplikaci Node

## <a name="configure-jenkins-plug-ins"></a>Konfigurace modulů plug-in Jenkins

Nejprve musíte nakonfigurovat dva moduly plug-in Jenkins: **NodeJS** a **VS Team Services Continuous Deployment**.

1. Otevřete svůj účet Jenkins a vyberte **Manage Jenkins** (Spravovat Jenkins).
2. Na stránce **Manage Jenkins** (Spravovat Jenkins) vyberte **Manage Plugins** (Spravovat moduly plug-in).
3. Filtrováním seznamu vyhledejte modul plug-in **NodeJS** a vyberte možnost **Install without restart** (Nainstalovat bez restartování).
    ![Přidání modulu plug-in NodeJS do Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Filtrováním seznamu vyhledejte modul plug-in **VS Team Services Continuous Deployment** a vyberte možnost **Install without restart** (Nainstalovat bez restartování).
5. Přejděte zpět na řídicí panel Jenkins a vyberte **Manage Jenkins** (Spravovat Jenkins).
6. Vyberte **Global Tool Configuration** (Globální konfigurace nástroje). Najděte **NodeJS** a vyberte **NodeJS installations** (Instalace NodeJS).
7. Vyberte možnost **Install automatically** (Nainstalovat automaticky) a pak zadejte hodnotu **Name** (Název).
8. Vyberte **Save** (Uložit).

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Konfigurace projektu Jenkins Freestyle pro Node.js

1. Vyberte **New Item** (Nová položka). Zadejte název položky.
2. Vyberte **Freestyle project** (Projekt Freestyle). Vyberte **OK**.
3. Na kartě **Source Code Management** (Správa zdrojového kódu) vyberte **Git** a zadejte podrobnosti o úložišti a větvi, které obsahují kód vaší aplikace.    
    ![Přidání úložiště do sestavení](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Na kartě **Build Triggers** (Aktivační události sestavení) vyberte **Poll SCM** (Cyklicky dotazovat Správce řízení služeb) a zadejte plán `H/03 * * * *`, který se bude úložiště Git dotazovat na změny každé tři minuty. 
5. Na kartě **Build Environment** (Prostředí sestavení) vyberte **Provide Node &amp; npm bin/ folder PATH** (Zadat cestu ke složce Node a npm bin/) a vyberte hodnotu **NodeJS Installation** (Instalace NodeJS). Možnost **npmrc file** (Soubor npmrc) nechejte nastavenou na **use system default** (použít výchozí nastavení systému).
6. Na kartě **Build** (Sestavení) vyberte **Execute shell** (Spustit prostředí) a zadáním příkazu `npm install` zajistěte aktualizaci všech závislostí.


## <a name="configure-jenkins-for-team-services-integration"></a>Konfigurace Jenkins pro integraci se službou Team Services

> [!NOTE]
> Zajistěte, aby token PAT, který použijete v následujících krocích, obsahoval ve službě Team Services oprávnění k *vydání verze* (čtení, zápis, spuštění a správa).
 
1.  Vytvořte token PAT v účtu služby Team Services, pokud ho ještě nemáte. Jenkins tyto informace vyžaduje pro přístup k vašemu účtu služby Team Services. Nezapomeňte informace tohoto tokenu uložit pro nadcházející kroky této části.
  
    Informace o vygenerován tokenu najdete v článku o [vytvoření tokenu PAT pro VSTS a TFS](https://www.visualstudio.com/docs/setup-admin/team-services/use-personal-access-tokens-to-authenticate).
2. Na kartě **Post-build Actions** (Akce po sestavení) vyberte **Add post-build action** (Přidat akci po sestavení). Vyberte **Archive the artifacts** (Archivovat artefakty).
3. Do pole **Files to archive** (Soubory k archivaci) zadejte `**/*`, čímž zahrnete všechny soubory.
4. Pokud chcete vytvořit další akci, vyberte **Add post-build action** (Přidat akci po sestavení).
5. Vyberte **Trigger release in TFS/Team Services** (Aktivovat vydanou verzi v TFS/Team Services). Zadejte identifikátor URI účtu služby Team Services, například **https://{název-vašeho-účtu}.visualstudio.com**.
6. Zadejte název pro **Team Project** (Týmový projekt).
7. Zvolte název definice verze. (Tuto definici verze vytvoříte později ve službě Team Services.)
8. Zvolte přihlašovací údaje pro připojení k prostředí Team Services nebo Team Foundation Serveru:
   - Pokud používáte službu Team Services, nechejte pole **Username** (Uživatelské jméno) prázdné. 
   - Pokud používáte místní verzi Team Foundation Serveru, zadejte uživatelské jméno a heslo.    
   ![Konfigurace akcí po sestavení v Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Uložte projekt Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Vytvoření koncového bodu služby Jenkins

Koncový bod služby umožňuje službě Team Services připojení k Jenkins.

1. Ve službě Team Services otevřete stránku **Služby**, otevřete seznam **Nový koncový bod služby** a vyberte **Jenkins**.
   ![Přidání koncového bodu Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Zadejte název tohoto připojení.
3. Zadejte adresu URL serveru Jenkins a vyberte možnost **Přijmout nedůvěryhodné certifikáty SSL**. Příklad adresy URL: **http://{vaše-adresa-URL-Jenkins}.westcentralus.cloudapp.azure.com**.
4. Zadejte uživatelské jméno a heslo svého účtu Jenkins.
5. Výběrem příkazu **Ověřit připojení** zkontrolujte, že jsou tyto údaje správné.
6. Výběrem tlačítka **OK** vytvořte koncový bod služby.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Vytvoření skupiny nasazení pro virtuální počítače Azure

Potřebujete [skupinu nasazení](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) pro registraci agenta služby Team Services, aby se definice verze dala nasadit do virtuálního počítače. Skupiny nasazení usnadňují definování logických skupin cílových počítačů pro nasazení a instalaci požadovaných agentů na jednotlivé počítače.

   > [!NOTE]
   > V následujícím postupu nainstalujte všechno, co je potřeba, a *nespouštějte skript s oprávněními sudo*.

1. Otevřete kartu **Vydání** centra **Sestavení a vydání**, otevřete **Skupiny nasazení** a vyberte **+ Nový**.
2. Zadejte název skupiny nasazení a volitelný popis. Potom vyberte **Vytvořit**.
3. Zvolte operační systém virtuálního počítače cíle nasazení. Vyberte například **Ubuntu 16.04+**.
4. Vyberte **Použít ve skriptu token PAT k ověřování**.
5. Vyberte odkaz **Systémové předpoklady**. Nainstalujte tyto předpoklady do operačního systému.
6. Výběrem příkazu **Zkopírovat skript do schránky** skript zkopírujte.
7. Přihlaste se k virtuálnímu počítači cíle nasazení a spusťte tento skript. Nespouštějte skript s oprávněními sudo.
8. Po instalaci budete vyzváni k zadání značek skupiny nasazení. Přijměte výchozí hodnoty.
9. Ve službě Team Services vyhledejte v seznamu **Cíle** v oblasti **Skupiny nasazení** nově registrovaný virtuální počítač.

## <a name="create-a-team-services-release-definition"></a>Vytvoření definice verze ve službě Team Services

Definice verze určuje proces, který služba Team Services používá k nasazení aplikace. V tomto příkladu spustíte skript prostředí.

Definici verze vytvoříte ve službě Team Services takto:

1. Otevřete kartu **Vydání** centra **Sestavení a vydání** a vyberte **Vytvořit definici verze**. 
2. Zvolte, že chcete začít **prázdným procesem** a vyberte **prázdnou** šablonu.
3. V části **Artefakty** vyberte **+ Přidat artefakt** a jako **Typ zdroje** zvolte **Jenkins**. Vyberte připojení ke koncovému bodu služby Jenkins. Pak vyberte zdrojovou úlohu Jenkins a vyberte **Přidat**.
4. Vyberte tři tečky vedle **Prostředí 1**. Vyberte **Přidat fázi skupiny nasazení**.
5. Zvolte skupinu nasazení.
5. Výběrem symbolu **+** přidejte do **Fáze skupiny nasazení** úlohu.
6. Vyberte úlohu **Skript prostředí** a pak vyberte **Přidat**. Úloha **Skript prostředí** zajišťuje konfiguraci skriptu, který má na jednotlivých serverech nainstalovat Node.js a spustit aplikaci.
8. Do pole **Cesta ke skriptu** zadejte **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Vyberte **Upřesnit** a povolte **Zadat pracovní adresář**.
10. Do pole **Pracovní adresář** zadejte **$(System.DefaultWorkingDirectory)/Fabrikam-Node**.
11. Název definice verze změňte na název, který jste pro sestavení v Jenkins zadali na kartě **Post-build Actions** (Akce po sestavení). Služba Jenkins tento název vyžaduje, aby mohla při aktualizaci zdrojových artefaktů aktivovat novou vydanou verzi.
12. Výběrem příkazů **Uložit** a **OK** definici verze uložte.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Ruční nasazení nebo nasazení aktivované přes CI

1. Vyberte **+ Vydaná verze** a vyberte **Vytvořit vydanou verzi**.
2. Ve zvýrazněném rozevíracím seznamu vyberte dokončené sestavení a pak vyberte **Zařadit do fronty**.
3. Ve vyskakovací zprávě zvolte odkaz na vydanou verzi. Příklad: „Vydaná verze **Verze-1** byla vytvořena.“
4. Otevřete kartu **Protokoly** a prohlédněte si výstup konzole vydané verze.
5. V prohlížeči otevřete adresu URL jednoho ze serverů, který jste přidali do skupiny nasazení. Zadejte například **http://{IP-adresa-vašeho-serveru}**.
6. Přejděte do zdrojového úložiště Gitu a změňte text nadpisu **h1** v souboru app/views/index.jade.
7. Potvrďte tuto změnu.
8. Po několika minutách uvidíte na stránce **Vydání** služby Team Services nebo Team Foundation Serveru vytvořenou novou vydanou verzi. Otevřete tuto vydanou verzi a zjistěte, jestli probíhá nasazení. Blahopřejeme!

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste automatizovali nasazení aplikace do Azure pomocí služby Jenkins pro sestavení a služby Team Services pro vydanou verzi. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Sestavení aplikace v Jenkins
> * Konfigurace Jenkins pro integraci se službou Team Services
> * Vytvoření skupiny nasazení pro virtuální počítače Azure
> * Vytvoření definice verze, která nakonfiguruje virtuální počítače a nasadí aplikaci

Pokud chcete zjistit, jak nasadit sadu LAMP (Linux, Apache, MySQL a PHP), přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Nasazení zásobníku LAMP](tutorial-lamp-stack.md)
