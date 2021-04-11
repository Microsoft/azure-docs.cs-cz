---
title: 'Rychlý Start: Vytvoření aplikace v Pythonu v Azure Portal'
description: Začněte s Azure App Service nasazením první aplikace v Pythonu do kontejneru Linux v App Service pomocí Azure Portal.
ms.topic: quickstart
ms.date: 04/01/2021
ms.custom: devx-track-python
robots: noindex
ms.openlocfilehash: 42f29152521da7bf90a550248e8429e51b728b24
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012352"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux-azure-portal"></a>Rychlý Start: Vytvoření aplikace v Pythonu pomocí Azure App Service v systému Linux (Azure Portal)

V tomto rychlém startu nasadíte webovou aplikaci v Pythonu, která se [App Service v systému Linux](overview.md#app-service-on-linux), vysoce škálovatelná služba s automatickými opravami pro hostování webů v Azure. Pomocí Azure Portal nasadíte ukázku s použitím této baňky nebo rozhraní Django. Webová aplikace, kterou nakonfigurujete, používá základní App Serviceovou vrstvu, u které se v předplatném Azure vyskytnou malé náklady.

## <a name="configure-accounts"></a>Konfigurace účtů

- Pokud ještě nemáte účet Azure s aktivním předplatným, [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Pokud nemáte účet GitHubu, přejděte na [GitHub.com](https://github.com) a vytvořte si ho. 

## <a name="fork-the-sample-github-repository"></a>Rozvětvení ukázkového úložiště GitHub

1. Otevřete [GitHub.com](https://github.com) a přihlaste se.

1. Přejděte k jednomu z následujících ukázkových úložišť:
    - [Hello World baňky](https://github.com/Azure-Samples/python-docs-hello-world)
    - [Django Hello World](https://github.com/Azure-Samples/python-docs-hello-django)

1. V pravém horním rohu stránky GitHubu vyberte možnost **rozvětvení** a vytvořte kopii úložiště ve vlastním účtu GitHub:

    ![Příkaz pro rozvětvení GitHubu](media/quickstart-python-portal/github-fork-command.png)

    Azure vyžaduje, abyste měli přístup k organizaci GitHubu, která obsahuje úložiště. Po rozvětvení ukázky na vlastní účet GitHubu budete mít k dispozici automaticky potřebný přístup a můžete také provádět změny kódu.

## <a name="provision-the-app-service-web-app"></a>Zřízení webové aplikace v App Service

Webová aplikace App Service je webový server, na který nasazujete svůj kód.

1. Otevřete Azure Portal [https://portal.azure.com](https://portal.azure.com) a v případě potřeby se přihlaste.

1. Na panelu hledání v horní části Azure Portal zadejte "App Service" a pak vyberte **App Services**.

    ![Panel hledání portálu a výběr App Service](media/quickstart-python-portal/portal-search-bar.png)

1. Na stránce **App Services** vyberte **+ Přidat**:

    ![Přidat App Service – příkaz](media/quickstart-python-portal/add-app-service.png)

1. Na stránce **vytvořit webovou aplikaci** proveďte následující akce:
    
    | Pole | Akce |
    | --- | --- |
    | Předplatné | Vyberte předplatné Azure, které chcete použít. |
    | Skupina prostředků | V rozevíracím seznamu vyberte **vytvořit novou** . V místní nabídce zadejte "AppService-PythonQuickstart" a vyberte **OK**. |
    | Name | Zadejte název, který je jedinečný v rámci všech Azure, obvykle s využitím kombinace vašich osobních nebo firemních názvů, jako je *Contoso-TestApp-123*. |
    | Publikovat | Vyberte **Kód**. |
    | Zásobník modulu runtime | Vyberte **Python 3,8**. |
    | Operační systém | Vyberte **Linux** (Python je podporován pouze na Linux). |
    | Oblast | Vyberte nejbližší oblast. |
    | Plán pro Linux | Vyberte App Service plán ukončení, nebo pomocí příkazu **vytvořit nový** vytvořte nový. Doporučujeme použít plán **Basic B1** . |

    ![Vytvoření formuláře webové aplikace na Azure Portal](media/quickstart-python-portal/create-web-app.png)

1. V dolní části stránky vyberte **zkontrolovat + vytvořit**, zkontrolujte podrobnosti a pak vyberte **vytvořit**.

1. Po dokončení zřizování vyberte **Přejít k prostředku** a přejděte na novou App Service stránku. Vaše webová aplikace v tomto okamžiku obsahuje pouze výchozí stránku, takže další krok nasazuje vzorový kód.

Máte problémy? [Dejte nám prosím jistotu](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="deploy-the-sample-code"></a>Nasazení ukázkového kódu

1. Na stránce webová aplikace na Azure Portal vyberte **centrum nasazení**:
    
    ![Příkaz centra nasazení v nabídce App Service](media/quickstart-python-portal/deployment-center-command.png)


1. Na stránce **centrum nasazení** vyberte kartu **Nastavení** , pokud ještě není otevřená:

    ![Karta Nastavení centra nasazení](media/quickstart-python-portal/deployment-center-settings-tab.png)

1. V části **zdroj** vyberte **GitHub** a pak ve formuláři **GitHubu** , který se zobrazí, proveďte následující akce:

    | Pole | Akce |
    | --- | --- |
    | Signed in as | Pokud jste se už přihlásili k GitHubu, přihlaste se hned nebo v případě potřeby vyberte **změnit účet* . |
    | Organizace | V případě potřeby vyberte organizaci GitHub. |
    | Repository | Vyberte název ukázkového úložiště, které jste předtím rozstavili, buď **Python-docs-Hello-World** (baněk), nebo **Python-docs-Hello-Django** (Django). |
    | Větvení. | Vyberte **Main (Hlavní**). |

    ![Zdrojová konfigurace GitHubu centra nasazení](media/quickstart-python-portal/deployment-center-configure-github-source.png)

1. V horní části stránky vyberte **Uložit** a použijte nastavení.:

    ![Uložení konfigurace zdroje GitHubu do centra nasazení](media/quickstart-python-portal/deployment-center-configure-save.png)

1. Vyberte kartu **protokoly** a zobrazte stav nasazení. Sestavení a nasazení ukázky a zobrazení dalších protokolů během procesu trvá několik minut. Po dokončení se v protokolech musí odrážet stav **úspěch (aktivní)**:

    ![Karta protokoly nasazení centra](media/quickstart-python-portal/deployment-center-logs.png)

Máte problémy? [Dejte nám prosím jistotu](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="browse-to-the-app"></a>Přechod do aplikace

1. Po dokončení nasazení vyberte v nabídce na levé straně **Přehled** a vraťte se tak na hlavní stránku webové aplikace.

1. Vyberte adresu **URL** , která obsahuje adresu webové aplikace:

    ![Adresa URL webové aplikace na stránce Přehled](media/quickstart-python-portal/web-app-url.png)

1. Ověřte, že je výstup aplikace "Hello, World!":

    ![Aplikace spuštěná po počátečním nasazení](media/quickstart-python-portal/web-app-first-deploy-results.png)

Máte problémy? Další informace najdete v části [Průvodce odstraňováním potíží](configure-language-python.md#troubleshooting). v opačném případě [nám dejte](https://aka.ms/FlaskPortalQuickstartHelp)informace.

## <a name="make-a-change-and-redeploy"></a>Provedení změny a opětovného nasazení

Vzhledem k tomu, že jste se připojili App Service k úložišti, změny, které zadáte do vašeho zdrojového úložiště, se automaticky nasadí do webové aplikace.

1. Změny můžete provádět přímo v rozvětvené úložišti na GitHubu, nebo můžete úložiště klonovat místně, udělat a potvrdit změny a pak tyto změny vložit do GitHubu. Kterákoli z metod má za následek změnu v úložišti, které je připojené k App Service.

1. **Ve svém rozvětvené úložišti** změňte zprávu aplikace z "Hello, World!". na Hello, Azure! následujícím způsobem:
    - Baňka (ukázka Pythonu-docs-Hello-World): změňte textový řetězec na řádku 6 souboru *Application.py* .
    - Django (ukázka Python-docs-Hello-Django): změňte textový řetězec na řádku 5 souboru *views.py* ve složce *Hello* .

1. Potvrďte změnu v úložišti.

    Pokud používáte místní klon, dodejte tyto změny také do GitHubu.

1. V Azure Portal pro webovou aplikaci se vraťte do **centra nasazení**, vyberte kartu **protokoly** a poznamenejte si novou aktivitu nasazení, která se má prohledat.

1. Až se nasazení dokončí, vraťte se na stránku **Přehled** webové aplikace, otevřete adresu URL webové aplikace znovu a sledujte změny v aplikaci:

    ![Aplikace spuštěná po opětovném nasazení](media/quickstart-python-portal/web-app-second-deploy-results.png)

Máte problémy? Další informace najdete v části [Průvodce odstraňováním potíží](configure-language-python.md#troubleshooting). v opačném případě [nám dejte](https://aka.ms/FlaskCLIQuickstartHelp)informace.

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků s názvem "AppService-PythonQuickstart", která se zobrazuje na stránce *Přehled* webové aplikace *. Pokud udržujete webovou aplikaci spuštěnou, bude se vám účtovat průběžné náklady (viz [ceny App Service](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, vyberte název skupiny prostředků na stránce **Přehled** webové aplikace a přejděte do části Přehled skupin prostředků. Vyberte **Odstranit skupinu prostředků** a postupujte podle zobrazených výzev.

![Odstranění skupiny prostředků](media/quickstart-python-portal/delete-resource-group.png)


Máte problémy? [Dejte nám prosím jistotu](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kurz: webová aplikace Python (Django) s PostgreSQL](/azure/developer/python/tutorial-python-postgresql-app-portal)

> [!div class="nextstepaction"]
> [Konfigurace aplikace v Pythonu](configure-language-python.md)

> [!div class="nextstepaction"]
> [Přidání přihlášení uživatele do webové aplikace v Pythonu](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Kurz: spuštění aplikace v Pythonu ve vlastním kontejneru](tutorial-custom-container.md)
