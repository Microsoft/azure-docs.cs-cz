---
title: Spustit objekt pro vytváření image z Azure DevOps v Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit objekt pro vytváření vlastní image ve službě Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: spelluru
ms.openlocfilehash: abb85d568e26e4b6f85b960a2560aae570daf201
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61320312"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Spuštění objektu pro vytváření imagí z Azure DevOps
Tento článek se týká všech přípravy potřebných ke spuštění objekt pro vytváření image z Azure DevOps (dřív Visual Studio Team Services).

> [!NOTE]
> Bude fungovat jakékoli orchestrační modul! Azure DevOps není povinné. Objekt pro vytváření image se spouští pomocí skriptů prostředí Azure PowerShell, takže je možné spustit ručně, pomocí plánovače úloh Windows, jinými systémy pro CI/CD a tak dále.

## <a name="create-a-lab-for-the-image-factory"></a>Vytvoření testovacího prostředí pro vytváření bitové kopie
K vytvoření testovacího prostředí ve službě Azure DevTest Labs je první krok při nastavení objekt pro vytváření bitové kopie. Toto testovací prostředí je lab objekt pro vytváření bitové kopie, můžeme vytvořit virtuální počítače a uložení vlastních imagí. Toto testovací prostředí je nepovažuje za součást celkový proces objekt pro vytváření bitové kopie. Po vytvoření testovacího prostředí, nezapomeňte si uložit název, protože ho budete potřebovat později.

## <a name="scripts-and-templates"></a>Skripty a šablony
Dalším krokem při přijímání objekt pro vytváření bitové kopie pro váš tým je pochopit, co je k dispozici. Jsou k dispozici veřejně v obrázku objekt pro vytváření skriptů a šablon [úložiště GitHub se vzorovými DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Tady je přehled části:

- Objekt pro vytváření bitové kopie. To je kořenová složka.
    - Konfigurace. Vstupy pro vytváření bitové kopie
        - GoldenImages. Tato složka obsahuje soubory JSON, které představují definice vlastních imagí.
        - Labs.json. Soubor, kde týmy zaregistrovat pro příjem konkrétních vlastních imagí.
- Skripty. Modul pro vytváření bitové kopie.

Články v této části poskytují další podrobnosti o těchto skriptů a šablon.

## <a name="create-an-azure-devops-team-project"></a>Vytvořit týmový projekt Azure DevOps
Azure DevOps umožňují ukládat zdrojový kód, spusťte rutinu prostředí Azure PowerShell na jednom místě. Můžete naplánovat opakované spuštění aktualizovat Image. Nejsou k dispozici dobré zařízení pro protokolování výsledků diagnostikovat problémy.  Pomocí Azure DevOps, ale není vyžadováno, můžete použít jakékoli prostředí/modul, který se můžete připojit k Azure a můžete spustit prostředí Azure PowerShell.

Pokud máte existující účet DevOps nebo projekt, který chcete použít místo toho, tento krok přeskočte.

Abyste mohli začít, vytvořte si bezplatný účet v Azure DevOps. Navštivte https://www.visualstudio.com/ a vyberte **začít zdarma** vpravo pod **Azure DevOps** (dříve VSTS). Budete muset Zvolte jedinečný název účtu a ujistěte se, že chcete spravovat kód pomocí Gitu. Jakmile se vytvoří, uložte adresu URL k vašemu týmovému projektu. Tady je ukázka adresy URL: `https://<accountname>.visualstudio.com/MyFirstProject`.

## <a name="check-in-the-image-factory-to-git"></a>Zkontrolujte v objektu pro vytváření bitové kopie na Git
Všechna prostředí PowerShell, šablony a konfigurace objektu pro vytváření bitové kopie jsou umístěny v [veřejného úložiště DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Nejrychlejší způsob, jak získat kód do nového týmového projektu je importovat do úložiště. Tím zajistíte, že v celé úložiště DevTest Labs (tak získáte navíc dokumentů a ukázek).

1. Navštivte projekt Azure DevOps, který jste vytvořili v předchozím kroku (adresa URL vypadá jako **https:\//\<accountname >.visualstudio.com/MyFirstProject**).
2. Vyberte **importovat úložiště**.
3. Zadejte **adresu URL klonování** úložiště DevTest Labs: `https://github.com/Azure/azure-devtestlab`.
4. Vyberte **Import**.

    ![Importovat úložiště Git](./media/set-up-devops-lab/import-git-repo.png)

Pokud se rozhodnete jenom vrátit se změnami přesně toho, co je potřeba (objekt pro vytváření souborů obrázků), postupujte podle kroků [tady](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) a naklonujte úložiště Git push jenom soubory umístěné ve **skripty/ImageFactory** adresáře.

## <a name="create-a-build-and-connect-to-azure"></a>Vytváření sestavení a připojte se k Azure
V tomto okamžiku máte zdrojové soubory uložené v úložišti Git v Azure DevOps. Nyní musíte nastavit kanál ke spuštění prostředí Azure PowerShell. Existuje mnoho možností proveďte tyto kroky. V tomto článku použít definici sestavení pro zjednodušení, ale funguje s DevOps sestavení, vydání DevOps (jeden nebo více prostředími), další spuštění enginy jako Plánovač úloh Windows nebo jiné prostředí, která může spustit prostředí Azure PowerShell.

> [!NOTE]
> Jeden důležitý bod mějte na paměti, že některé soubory Powershellu trvat dlouhou dobu spustit, když jsou mnohem (více než 10) vlastní Image k vytvoření. Bezplatné hostovaných agentů DevOps Buildů a vydání mají časový limit 30 minut, proto nelze použít bezplatný hostovaný agent, jakmile začnete vytvářet celou řadu imagí. Tuto výzvu vypršení časového limitu se vztahuje na libovolné harness rozhodnete použít, je vhodné ověřit, že můžete rozšířit typické vypršení časového limitu pro dlouho běžící skripty Azure Powershellu ještě před zahájením. V případě Azure DevOps můžete buď použít placené hostovaných agentů nebo pomocí vlastního agenta sestavení.

1. Pokud chcete začít, vyberte **nastavení sestavení** na domovské stránce projektu DevOps:

    ![Tlačítko Nastavení sestavení](./media/set-up-devops-lab/setup-build-button.png)
2. Zadejte **název** pro sestavení (například: Vytvářejte a dodávejte bitové kopie k DevTest Labs).
3. Vyberte **prázdný** definice sestavení a vyberte **použít** k vytvoření sestavení.
4. V této fázi můžete zvolit **hostovaná** agenta sestavení.
5. **Uložit** definici sestavení.

    ![Definice sestavení](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Konfigurace proměnných sestavení
Pro zjednodušení parametry příkazového řádku, zapouzdření klíčové hodnoty, které řídí objekt pro vytváření image pro sadu proměnných sestavení. Vyberte **proměnné** kartu a zobrazí se seznam několika výchozí proměnné. Tady je seznam proměnných, které zadáte v Azure DevOps:


| Název proměnné | Hodnota | Poznámky |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | Jedná se o úplnou cestu v úložišti, abyste **konfigurace** složky. Pokud jste naimportovali celé úložiště výše, hodnotu na levé straně je správná. Jinak aktualizujte tak, aby odkazoval na umístění konfigurace. |
| DevTestLabName | MyImageFactory | Název testovacího prostředí ve službě Azure DevTest Labs k vytvoření Image použít jako objekt pro vytváření. Pokud ho nemáte, vytvořte si ho. Ujistěte se, že je testovací prostředí v rámci stejného předplatného, který má přístup ke koncovému bodu služby. |
| ImageRetention | 1 | Počet imagí, které chcete uložit každého typu. Nastavte výchozí hodnotu na 1. |
| MachinePassword | ******* | Integrované heslo správce pro virtuální počítače. Jedná se o přechodné účet, proto se ujistěte, že je zabezpečené. Vyberte malou ikonu zámku na pravé straně zajistit, že se že jedná zabezpečený řetězec. |
| MachineUserName | ImageFactoryUser | Integrované účet uživatelské jméno správce pro virtuální počítače. Jedná se o přechodné účet. |
| StandardTimeoutMinutes | 30 | Časový limit, kterou jsme čekat standardních operací Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | ID předplatného, ve kterém testovacím prostředí existuje a zda má přístup ke koncovému bodu služby. |
| VMSize | Standard_A3 | Velikost virtuálního počítače pro použití **vytvořit** kroku. Virtuální počítače vytvořené jsou přechodné. Velikost musí být takový, která [povolené pro testovací prostředí](devtest-lab-set-lab-policy.md). Potvrdit, že máte dostatek [kvótu jader předplatného](../azure-subscription-service-limits.md).

![Vytvoření proměnné](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Připojení k Azure
Dalším krokem je nastavení instančního objektu služby Azure. Toto je identita v Azure Active Directory, která umožňuje agent sestavení DevOps v Azure provozují jménem uživatele. Ho nastavit začněte s přidáváním Azure Powershellu vytvořit nejdřív.

1. Vyberte **přidat úkol**.
2. Vyhledejte **prostředí Azure PowerShell**.
3. Jakmile pro vás, vyberte **přidat** přidání úkolu do sestavení. Když to uděláte, zobrazí se vám na levé straně se zobrazí při přidání úlohy.

![Nastavení prostředí PowerShell kroku](./media/set-up-devops-lab/set-up-powershell-step.png)

Nejrychlejší způsob, jak nastavit službu objektu zabezpečení je nechat DevOps Azure to udělá za nás.

1. Vyberte **úloh** jste právě přidali.
2. Pro **typ připojení Azure**, zvolte **Azure Resource Manageru**.
3. Vyberte **spravovat** odkaz na nastavení instančního objektu.

Další informace najdete v tomto [blogový příspěvek](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Když vyberete **spravovat** odkazu, budete přesměrováni na správném místě v DevOps (druhý snímek obrazovky v blogovém příspěvku) k nastavení připojení k Azure. Nezapomeňte vybrat **koncového bodu služby Azure Resource Manageru** při nastavování.

## <a name="complete-the-build-task"></a>Dokončení úlohy sestavení
Pokud vyberete úlohu sestavení, zobrazí se vám všechny podrobnosti v pravém podokně, které by mělo být vyplněno.

1. Jméno úkol sestavení: **Vytvoření virtuálních počítačů**.
2. Zvolte **instanční objekt služby** vytvoříte výběrem **Azure Resource Manageru**
3. Zvolte **koncový bod služby**.
4. Pro **cesta ke skriptu**vyberte **... (tři tečky)**  na pravé straně.
5. Přejděte do **MakeGoldenImageVMs.ps1** skriptu.
6. Parametry skriptu by měl vypadat nějak takto: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Dokončení definice sestavení](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Zařaďte sestavení do fronty
Pojďme ověřit, že máte všechno správně nastavené zařazením do nového sestavení. Je spuštěn sestavení, přepněte [webu Azure portal](https://portal.azure.com) a vyberte na **všechny virtuální počítače** ve vaší laboratoři objekt pro vytváření bitové kopie pro potvrzení, že všechno funguje správně. Měli byste vidět, že tři virtuální počítače vytvořené v testovacím prostředí.

![Virtuální počítače v testovacím prostředí](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Další postup
Prvním krokem při nastavení objekt pro vytváření bitové kopie založené na službě Azure DevTest Labs je dokončena. V dalším článku v této sérii získáte ty virtuální počítače generalizovaných a uloží do vlastních imagí. Pak bude nutné je distribuován do všech dalších laboratoří. Zobrazit další článek v sérii: [Uložení vlastních imagí a distribuovat do více labs](image-factory-save-distribute-custom-images.md).
