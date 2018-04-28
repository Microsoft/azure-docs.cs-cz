---
title: Použití Azure kontejner instancí jako volaných sestavení agenta
description: Další informace o použití instancí kontejnerů Azure jako volaných sestavení agenta.
services: container-instances
author: neilpeterson
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/20/2018
ms.author: nepeters
ms.openlocfilehash: dbe418db8fb3d73739a30b60703f15b3dc47b291
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="use-azure-container-instances-as-a-jenkins-build-agent"></a>Použití Azure kontejner instancí jako volaných sestavení agenta

Kontejner Azure instancí poskytují na vyžádání, burstable a izolované prostředí pro spuštění kontejnerizované úlohy. Z důvodu těchto atributů zkontrolujte Azure kontejner instancí vynikající platformu pro spuštění úlohy sestavení volaných ve velkém měřítku. Tento dokument vás provede nasazení a používání volaných server, který je předem nakonfigurovaný s ACI jako cíl sestavení.

Další informace o kontejneru instance Azure, najdete v části [o instancí kontejnerů Azure][about-aci].

## <a name="deploy-jenkins-server"></a>Nasazení serveru volaných

Na portálu Azure vyberte **vytvořit prostředek** a vyhledejte **volaných**. Vyberte nabídku volaných se vydavatele **Microsoft** a vyberte **vytvořit**.

Zadejte následující informace ve formuláři základní informace a klikněte na tlačítko **OK** po dokončení.

- **Název** – název volaných nasazení.
- **Uživatelské jméno** -toto uživatelské jméno pro virtuální počítač volaných slouží jako uživatel s oprávněními správce.
- **Typ ověřování** – SSH se doporučuje veřejný klíč. Pokud vyberete, zkopírujte veřejný klíč SSH, který se má použít při přihlašování do volaných virtuálního počítače.
- **Předplatné** -vyberte předplatné Azure.
- **Skupina prostředků** – vytvořte novou nebo vyberte existující skupinu prostředků.
- **Umístění** -vyberte umístění pro volaných server.

![Základní nastavení volaných portálu nasazení](./media/container-instances-jenkins/jenkins-portal-01.png)

Ve formuláři další nastavení proveďte následující položky:

- **Velikost** -vyberte možnost odpovídající velikosti vašeho volaných virtuálního počítače.
- **Typ disku virtuálního počítače** -zadejte pevný disk (jednotku pevného disku) nebo SSD (solid-state jednotka) pro server volaných.
- **Virtuální síť** – (volitelné) vyberte virtuální síť můžete upravit výchozí nastavení.
- **Podsítě** – vyberte podsítě, zkontrolujte zadané informace a vyberte **OK**.
- **Veřejná IP adresa** -výběr veřejnou IP adresu můžete použít vlastní název a nakonfigurovat SKU a metoda přiřazení.
- **Popisek názvu domény** – zadejte hodnotu pro vytvoření plně kvalifikovanou adresu URL pro virtuální počítač volaných.
- **Typ verze volaných** – typ požadovanou verzi, vyberte z možností: LTS, týdenní sestavení nebo ověřit Azure.

![Další nastavení volaných portálu nasazení](./media/container-instances-jenkins/jenkins-portal-02.png)

Hlavní integrace služby, vyberte **Auto(MSI)** tak, aby měl [identita spravované služby Azure] [ managed-service-identity] automaticky vytvořit identitu ověřování pro volaných instance. Vyberte ruční poskytovateli svoje vlastní přihlašovací údaje hlavní služby.

Agenti cloudu nakonfigurujte Cloudová platforma pro volaných sestavení úlohy. Z důvodu tohoto dokumentu vyberte ACI. S agentem ACI cloudu Každá úloha sestavení volaných běží v instanci Azure kontejneru.

![Nastavení integrace do cloudu volaných portálu nasazení](./media/container-instances-jenkins/jenkins-portal-03.png)

Po dokončení nastavení integrace, klikněte na **OK**a potom **OK** znovu v souhrnu ověření. Klikněte na tlačítko **vytvořit** na podmínky použití souhrn. Server volaných trvá několik minut pro nasazení.

## <a name="configure-jenkins"></a>Konfigurace Jenkinse

Na portálu Azure přejděte do skupiny prostředků volaných, vyberte virtuální počítač volaných a poznamenejte si název DNS.

![Pokyny volaných přihlášení](./media/container-instances-jenkins/jenkins-portal-fqdn.png)

Prohlížeč, název DNS volaných virtuálních počítačů a zkopírujte vrácený řetězec SSH.

![Pokyny volaných přihlášení](./media/container-instances-jenkins/jenkins-portal-04.png)

Otevře relaci Terminálové ve vývojovém systému a vložte řetězec SSH z poslední krok. Zadané uživatelské jméno při nasazování serveru volaných aktualizujte 'uživatelského jména'.

Po připojení, spusťte následující příkaz k načtení hesla počáteční správce.

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Nechte relace SSH a tunelové propojení systémem a přejděte do http://localhost:8080 v prohlížeči. Heslo správce počáteční vložte do pole, jak je vidět na následujícím obrázku. Vyberte **pokračovat** po dokončení.

![Odemknutí volaných](./media/container-instances-jenkins/jenkins-portal-05.png)

Vyberte **nainstalovat navrhované modulů plug-in** nainstalujte všechny doporučené volaných modulů plug-in.

![Instalace modulu plug-in volaných](./media/container-instances-jenkins/jenkins-portal-06.png)

Vytvořte nový uživatelský účet správce. Tento účet se používá pro přihlašování a práci s vaší instancí volaných.

![Vytvoření volaných uživatele](./media/container-instances-jenkins/jenkins-portal-07.png)

Vyberte **uložit a dokončení** až budete hotoví a potom **začít používat volaných** k dokončení konfigurace.

Volaných je nyní nakonfigurována a připravena k vytvoření a nasazení kódu. V tomto příkladu jednoduchou aplikaci Java slouží k předvedení volaných sestavení v instancích kontejner Azure.

## <a name="create-build-job"></a>Vytvoření úlohy sestavení

Když pomocí bitové kopie kontejneru jako volaných sestavení target, budete muset zadat obrázek, který obsahuje všechny tooling potřebné pro úspěšné sestavení. Pokud chcete zadat bitovou kopii, vyberte **spravovat volaných** > **nakonfigurujte systém** a přejděte dolů k položce **cloudu** části. V tomto příkladu aktualizujte hodnotu Docker bitové kopie do `microsoft/java-on-azure-jenkins-slave`.

Po dokončení klikněte na tlačítko **Uložit** vrátit na řídicí panel volaných.

![Konfigurace cloudu volaných](./media/container-instances-jenkins/jenkins-aci-image.png)

Nyní vytvoří úlohu volaných sestavení. Vyberte **nová položka**, zadejte například název sestavení projektu `aci-java-demo`, vyberte **volný styl projektu**a klikněte na tlačítko **OK**.

![Vytvoření úlohy Jenkinse](./media/container-instances-jenkins/jenkins-new-job.png)

V části **Obecné**, ujistěte se, že **omezit, kde můžete spustit tento projekt** je vybrána. Zadejte `linux` pro výraz popisku. Tato konfigurace zajišťuje, že tato úloha sestavení běží v cloudu ACI.

![Vytvoření úlohy Jenkinse](./media/container-instances-jenkins/jenkins-job-01.png)

V části správu zdrojového kódu, vyberte `Git` a zadejte `https://github.com/spring-projects/spring-petclinic.git` pro adresu URL úložiště. Toto úložiště GitHub obsahuje ukázkový kód aplikace.

![Přidejte do úlohy volaných zdrojového kódu](./media/container-instances-jenkins/jenkins-job-02.png)

V části sestavení, vyberte **přidejte krok sestavení** a vyberte `Invoke top-level Maven targets`. Zadejte `package` jako cíl krok sestavení.

![Přidejte že krok volaných sestavení](./media/container-instances-jenkins/jenkins-job-03.png)

Vyberte **Uložit** po dokončení.

## <a name="run-the-build-job"></a>Spustit úlohu sestavení

Testovací úloha sestavení a sledovat instancí kontejnerů Azure jako platformu sestavení, spusťte ručně sestavení.

Vyberte **sestavení teď** při spuštění úlohy sestavení. Jak dlouho trvá několik minut na spuštění při spuštění úlohy, byste měli vidět stav podobná následující bitové kopie.

![Přidejte že krok volaných sestavení](./media/container-instances-jenkins/jenkins-job-status.png)

Když úloha běží, otevřete portál Azure a podívejte se na skupině prostředků volaných. Měli byste vidět, vytvořený kontejner instancí Azure. Je v této instanci, která je spuštěná úloha volaných.

![Sestavení volaných v ACI](./media/container-instances-jenkins/jenkins-aci.png)

Volaných běží víc úloh než nakonfigurované počet volaných vykonavatelů (výchozí 2), vytváření více instancí kontejneru Azure.

![Sestavení volaných v ACI](./media/container-instances-jenkins/jenkins-aci-multi.png)

Po dokončení všech úloh sestavení, budou odstraněny instance Azure kontejneru.

![Sestavení volaných v ACI](./media/container-instances-jenkins/jenkins-aci-none.png)

## <a name="next-steps"></a>Další postup

Další informace o volaných na Azure najdete [Azure a volaných][jenkins-azure].

<!-- LINKS - internal -->
[about-aci]: ./container-instances-overview.md
[jenkins-azure]: ../jenkins/overview.md
[managed-service-identity]: ../active-directory/managed-service-identity/overview.md