---
title: Jenkinse Build na instanci kontejneru
description: Naučte se konfigurovat server Jenkinse pro spouštění úloh sestavení na vyžádání v Azure Container Instances
ms.topic: article
ms.date: 08/31/2018
ms.openlocfilehash: 8bb84895fb581053248fbad326ea7b2c8d1873a2
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617967"
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Použití Azure Container Instances jako agenta sestavení Jenkinse

Azure Container Instances (ACI) poskytuje prostředí, které je na vyžádání k dispozici, a izolované prostředí pro spouštění kontejnerových úloh. Z důvodu těchto atributů ACI vytváří skvělou platformu pro spouštění úloh Jenkinse buildu ve velkém měřítku. Tento článek vás provede nasazením a použitím serveru Jenkinse, který je předem nakonfigurovaný s ACI jako cíl sestavení.

Další informace o Azure Container Instances najdete v tématu [o Azure Container Instances](../container-instances/container-instances-overview.md).

## <a name="deploy-a-jenkins-server"></a>Nasazení serveru Jenkinse

1. V Azure Portal vyberte **vytvořit prostředek** a vyhledejte **Jenkinse**. Vyberte nabídku Jenkinse s vydavatelem **Microsoftu**a pak vyberte **vytvořit**.

2. Do formuláře **základy** zadejte následující informace a pak vyberte **OK**.

   - **Název**: zadejte název pro nasazení Jenkinse.
   - **Uživatelské jméno**: Zadejte jméno uživatele Správce virtuálního počítače Jenkinse.
   - **Typ ověřování**: pro ověřování doporučujeme použít veřejný klíč SSH. Pokud vyberete tuto možnost, vložte ji do veřejného klíče SSH, který se použije k přihlášení k virtuálnímu počítači Jenkinse.
   - **Předplatné:** Vyberte předplatné Azure.
   - **Skupina prostředků:** Vytvořte skupinu prostředků nebo vyberte stávající.
   - **Umístění**: vyberte umístění pro server Jenkinse.

   ![Základní nastavení pro nasazení portálu Jenkinse](./media/container-instances-jenkins/jenkins-portal-01.png)

3. Ve formuláři **Další nastavení** proveďte následující položky:

   - **Velikost**: vyberte vhodnou možnost změny velikosti pro virtuální počítač s Jenkinse.
   - **Typ disku virtuálního počítače**: zadejte na server **Jenkinse buď pevný** disk (jednotku pevného disku), nebo **SSD** (Solid-State Drive).
   - **Virtuální síť**: tuto šipku vyberte, pokud chcete změnit výchozí nastavení.
   - **Podsítě**: vyberte šipku, zkontrolujte informace a vyberte **OK**.
   - **Veřejná IP adresa**: výběrem šipky udělte veřejné IP adrese vlastní název, nakonfigurujte SKU a nastavte metodu přiřazení.
   - **Popisek názvu domény**: zadejte hodnotu, která vytvoří plně KVALIFIKOVANOU adresu URL virtuálního počítače Jenkinse.
   - **Typ verze Jenkinse**: vyberte požadovaný typ vydání z možností: **LTS**, **týdenní Build**nebo **Azure ověřený**.

   ![Další nastavení pro nasazení portálu Jenkinse](./media/container-instances-jenkins/jenkins-portal-02.png)

4. Pro integraci instančního objektu vyberte **automaticky (MSI)** , aby měly [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) automaticky vytvořit identitu ověřování pro instanci Jenkinse. Pokud chcete zadat vlastní přihlašovací údaje instančního objektu, vyberte **ručně** .

5. Cloudové agenti konfigurují cloudovou platformu pro úlohy sestavení Jenkinse. Pro účely tohoto článku vyberte možnost **ACI**. V případě cloudového agenta ACI se každou úlohu sestavení Jenkinse spustí v instanci kontejneru.

   ![Nastavení integrace cloudu pro nasazení portálu Jenkinse](./media/container-instances-jenkins/jenkins-portal-03.png)

6. Až budete s nastavením integrace hotovi, vyberte **OK**a pak znovu vyberte **OK** v souhrnu ověření. V souhrnu **podmínky použití** vyberte **vytvořit** . Nasazení serveru Jenkinse trvá několik minut.

## <a name="configure-jenkins"></a>Konfigurace Jenkinse

1. V Azure Portal přejděte do skupiny prostředků Jenkinse, vyberte virtuální počítač Jenkinse a poznamenejte si název DNS.

   ![Název DNS v podrobnostech o virtuálním počítači s Jenkinse](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

2. Vyhledejte název DNS virtuálního počítače Jenkinse a zkopírujte vrácený řetězec SSH.

   ![Jenkinse přihlašovací pokyny k řetězci SSH](./media/container-instances-jenkins/jenkins-portal-04.png)

3. Ve vývojovém systému otevřete relaci Terminálové služby a vložte ji do řetězce SSH z posledního kroku. Aktualizujte `username` na uživatelské jméno, které jste zadali při nasazení serveru Jenkinse.

4. Po připojení relace spusťte následující příkaz, který načte počáteční heslo správce:

   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```

5. Ponechte relaci SSH a tunelové propojení spuštěné a v prohlížeči přejít na `http://localhost:8080`. Vložte do pole počáteční heslo správce a pak vyberte **pokračovat**.

   ![Obrazovka "odemknout Jenkinse" s polem pro heslo správce](./media/container-instances-jenkins/jenkins-portal-05.png)

6. Vyberte **instalovat navrhované moduly plug-in** a nainstalujte všechny doporučené moduly plug-in Jenkinse.

   ![Na obrazovce "přizpůsobení Jenkinse" se vybere volba nainstalovat navrhované moduly plug-in.](./media/container-instances-jenkins/jenkins-portal-06.png)

7. Vytvořte účet uživatele s právy pro správu. Tento účet se používá pro přihlášení k instanci Jenkinse a práci s ní.

   ![Obrazovka "vytvořit prvního uživatele správce" s vyplněnými přihlašovacími údaji](./media/container-instances-jenkins/jenkins-portal-07.png)

8. Vyberte **Uložit a dokončit**a pak vyberte **začít používat Jenkinse** k dokončení konfigurace.

Jenkinse je teď nakonfigurovaný a připravený k sestavování a nasazování kódu. V tomto příkladu se k předvedení Jenkinse sestavení v Azure Container Instances používá jednoduchá aplikace Java.

## <a name="create-a-build-job"></a>Vytvořit úlohu sestavení

Nyní je vytvořena úloha sestavení Jenkinse, která předvádí Jenkinse sestavení na instanci kontejneru Azure.

1. Vyberte možnost **Nová položka**, zadejte název projektu sestavení jako **ACI-demo**, vyberte **projekt Freestyle**a vyberte **OK**.

   ![Box pro název úlohy sestavení a seznam typů projektů](./media/container-instances-jenkins/jenkins-new-job.png)

2. V části **Obecné**ověřte, zda je vybrána možnost **omezit, kde lze tento projekt spustit** . Jako výraz popisku zadejte **Linux** . Tato konfigurace zajišťuje, že tato úloha sestavení běží v cloudu ACI.

   ![Karta Obecné s podrobnostmi konfigurace](./media/container-instances-jenkins/jenkins-job-01.png)

3. V části **sestavení**vyberte **Přidat krok sestavení** a vyberte **spustit prostředí**. Jako příkaz zadejte `echo "aci-demo"`.

   ![Karta sestavení s výběry pro krok sestavení](./media/container-instances-jenkins/jenkins-job-02.png)

5. Vyberte **Save** (Uložit).

## <a name="run-the-build-job"></a>Spustit úlohu sestavení

Chcete-li otestovat úlohu sestavení a sledovat Azure Container Instances jako platformu sestavení, ručně spusťte sestavení.

1. Vyberte **sestavit hned** a spusťte úlohu sestavení. Spuštění úlohy trvá několik minut. Měl by se zobrazit stav podobný následujícímu obrázku:

   ![Informace o "historii sestavení" s stavem úlohy](./media/container-instances-jenkins/jenkins-job-status.png)

2. Když je úloha spuštěná, otevřete Azure Portal a podívejte se na skupinu prostředků Jenkinse. Měla by se zobrazit, že se vytvořila instance kontejneru. Úloha Jenkinse je spuštěná uvnitř této instance.

   ![Instance kontejneru ve skupině prostředků](./media/container-instances-jenkins/jenkins-aci.png)

3. Protože Jenkinse spustí více úloh, než je nakonfigurovaný počet Jenkinse prováděcích modulů (výchozí hodnota 2), vytvoří se několik instancí kontejnerů.

   ![Nově vytvořené instance kontejneru](./media/container-instances-jenkins/jenkins-aci-multi.png)

4. Po dokončení všech úloh sestavení se instance kontejnerů odeberou.

   ![Skupina prostředků s odebranými instancemi kontejneru](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Průběžná integrace a doručování do služby Azure App Service](java-deploy-webapp-tutorial.md)