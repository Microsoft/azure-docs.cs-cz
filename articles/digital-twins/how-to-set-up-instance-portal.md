---
title: Nastavení instance a ověřování (portál)
titleSuffix: Azure Digital Twins
description: Podívejte se, jak nastavit instanci služby Azure Digital pomocí Azure Portal
author: baanders
ms.author: baanders
ms.date: 7/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 12b0dd957cb89fc7114c752312b08f1e490499da
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89280022"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-portal"></a>Nastavení instance a ověřování digitálních vláken Azure (portál)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

Tento článek popisuje postup **Nastavení nové instance digitálního vlákna Azure**, včetně vytvoření instance a nastavení ověřování. Po dokončení tohoto článku budete mít instanci digitálních vláken Azure, která je připravená na zahájení programování.

Tato verze tohoto článku prochází těmito kroky ručně, jednou po jednom, pomocí Azure Portal. Azure Portal je unifikovaná webová konzola, která poskytuje alternativu k nástrojům příkazového řádku.
* Pokud chcete projít tyto kroky ručně pomocí rozhraní příkazového řádku, přečtěte si verzi rozhraní příkazového řádku tohoto článku: [*Postupy: nastavení instance a ověřování (CLI)*](how-to-set-up-instance-cli.md).
* Chcete-li provést automatickou instalaci pomocí skriptu nasazení, přečtěte si skriptovaná verze tohoto článku: [*Postupy: nastavení instance a ověřování (skriptované)*](how-to-set-up-instance-scripted.md).

[!INCLUDE [digital-twins-setup-steps-prereq.md](../../includes/digital-twins-setup-steps-prereq.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Vytvoření instance digitálních vláken Azure

V této části **vytvoříte novou instanci digitálních vláken Azure** pomocí [Azure Portal](https://ms.portal.azure.com/). Přejděte na portál a přihlaste se pomocí svých přihlašovacích údajů.

Na portálu Začněte výběrem možnosti _vytvořit prostředek_ v nabídce domovské stránky služeb Azure.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-resource.png" alt-text="Na domovské stránce Azure Portal vyberte vytvořit prostředek.":::

Ve vyhledávacím poli vyhledejte *digitální vlákna Azure* a z výsledků vyberte službu **Azure Digital revlákens (Preview)** . Kliknutím na tlačítko _vytvořit_ vytvořte novou instanci služby.

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins.png" alt-text="Výběr možnosti ' vytvořit ' na stránce služby Azure Digital Service":::

Na následující stránce *vytvořit prostředek* vyplňte hodnoty uvedené níže:
* **Předplatné**: předplatné Azure, které používáte
  - **Skupina prostředků**: Skupina prostředků, do které se má instance nasadit. Pokud ještě nemáte existující skupinu prostředků, můžete si ji vytvořit tak, že vyberete odkaz *vytvořit nový* a zadáte název nové skupiny prostředků.
* **Umístění**: oblast s povolenými digitálními podtechnologiemi Azure pro nasazení. Další podrobnosti o místní podpoře najdete v [*produktech Azure dostupných v oblasti (digitální vlákna Azure)*](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins).
* **Název prostředku**: název vaší instance digitálního vlákna Azure. Název nové instance musí být v rámci vašeho předplatného jedinečný (to znamená, že pokud má vaše předplatné jinou instanci digitálních vláken Azure v oblasti, která už používá zvolený název, zobrazí se výzva k výběru jiného názvu).

:::image type="content" source= "media/how-to-set-up-instance/portal/create-azure-digital-twins-2.png" alt-text="Naplnění popsaných hodnot k vytvoření prostředku digitálních vláken Azure":::

Po dokončení vyberte _zkontrolovat + vytvořit_. Tím přejdete na stránku Souhrn, kde můžete zkontrolovat podrobnosti instance, které jste zadali, a _vytvořit_. 

### <a name="verify-success-and-collect-important-values"></a>Ověřit úspěšné a shromažďovat důležité hodnoty

Po nahrání *Vytvoření*můžete zobrazit stav nasazení vaší instance v oznámeních Azure na panelu ikon portálu. Oznámení bude označovat, že nasazení proběhlo úspěšně, a budete moci vybrat tlačítko _Přejít k prostředku_ a zobrazit vytvořenou instanci.

:::image type="content" source="media/how-to-set-up-instance/portal/notifications-deployment.png" alt-text="Zobrazení oznámení Azure znázorňující úspěšné nasazení a zvýraznění tlačítka přejít na prostředek":::

Případně, pokud se nasazení nezdaří, oznámení indikuje, proč. Sledujte Rady z chybové zprávy a zkuste instanci vytvořit znovu.

>[!TIP]
>Po vytvoření instance se můžete kdykoli vrátit na svou stránku tak, že na panelu hledání Azure Portal vyhledáte název vaší instance.

Na stránce *Přehled* instance si poznamenejte její *název*, *skupinu prostředků*a *název hostitele*. Jedná se o všechny důležité hodnoty, které možná budete potřebovat, když budete pokračovat v práci s vaší instancí digitálních vláken Azure. Pokud budou jiné uživatele programovat s instancí, měli byste je s těmito hodnotami sdílet.

:::image type="content" source="media/how-to-set-up-instance/portal/instance-important-values.png" alt-text="Zvýrazňování důležitých hodnot na stránce přehledu instance":::

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k přechodu. V dalším kroku poskytnete příslušnému uživateli Azure oprávnění ke správě.

## <a name="set-up-user-access-permissions"></a>Nastavení uživatelských oprávnění pro přístup

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

Nejdřív otevřete stránku pro instanci digitálních vláken Azure v Azure Portal. V nabídce instance vyberte *řízení přístupu (IAM)*. V části *Přidat přiřazení role*vyberte tlačítko *Přidat* .

:::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-1.png" alt-text="Výběr přidání přiřazení role ze stránky řízení přístupu (IAM)":::

Na následující stránce *Přidat přiřazení role* vyplňte hodnoty (musí být dokončené uživatelem s [dostatečnými oprávněními](#prerequisites-permission-requirements) v předplatném Azure):
* **Role**: v rozevírací nabídce vyberte *vlastníka digitálních vláken Azure (Preview)* .
* **Přiřadit přístup k**: vyberte *uživatele, skupinu nebo INSTANČNÍ objekt Azure AD* z rozevírací nabídky.
* **Vyberte**: Vyhledejte jméno nebo e-mailovou adresu uživatele, který chcete přiřadit. Když vyberete výsledek, uživatel se zobrazí v části *Vybrané členy* .

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-set-up-instance/portal/add-role-assignment-2.png" alt-text="Naplnění uvedených polí do dialogového okna Přidat přiřazení role":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Až skončíte s zadáváním podrobností, stiskněte tlačítko *Uložit* .

### <a name="verify-success"></a>Ověřit úspěch

Můžete zobrazit přiřazení role, které jste nastavili v části *řízení přístupu (IAM) > přiřazení rolí*. Uživatel by se měl zobrazit v seznamu s rolí *vlastníka digitálních vláken Azure (Preview)*. 

:::image type="content" source="media/how-to-set-up-instance/portal/verify-role-assignment.png" alt-text="Zobrazení přiřazení rolí pro instanci digitálních vláken Azure v Azure Portal":::

Teď máte k dispozici instanci digitálních vláken Azure, která je připravená k použití, a máte přiřazená oprávnění ke správě IT. Potom nastavíte oprávnění pro klientské aplikace pro přístup k ní.

## <a name="set-up-access-permissions-for-client-applications"></a>Nastavení přístupových oprávnění pro klientské aplikace

[!INCLUDE [digital-twins-setup-app-registration.md](../../includes/digital-twins-setup-app-registration.md)]

Začněte tím, že přejdete na [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) v Azure Portal (můžete použít tento odkaz nebo ho najít pomocí panelu hledání na portálu). V nabídce služba vyberte *Registrace aplikací* a potom klikněte na *+ Nová registrace*.

:::image type="content" source="media/how-to-set-up-instance/portal/new-registration.png" alt-text="Zobrazení stránky služby Azure AD v Azure Portal, zvýraznění možnosti nabídky Registrace aplikací a + nová registrace":::

Na následující stránce *Registrovat aplikaci* vyplňte požadované hodnoty:
* **Název**: zobrazovaný název aplikace Azure AD, který se má přidružit k registraci.
* **Podporované typy účtů**: vyberte *účty jenom v tomto organizačním adresáři (jenom výchozí adresář – jeden tenant)* .
* **Identifikátor URI pro přesměrování**: *Adresa URL pro odpověď aplikace Azure AD* pro aplikaci Azure AD. Přidejte identifikátor URI *veřejného klienta/nativního (mobilní & Desktop)* pro `http://localhost` .

Až skončíte, stiskněte tlačítko *zaregistrovat* .

:::image type="content" source="media/how-to-set-up-instance/portal/register-an-application.png" alt-text="Zobrazení stránky registrace aplikace s popsanými hodnotami, které jsou vyplněny":::

Po dokončení nastavení registrace bude portál přesměrován na stránku podrobností.

### <a name="provide-azure-digital-twins-api-permission"></a>Poskytnutí oprávnění rozhraní API pro digitální vlákna Azure

V dalším kroku nakonfigurujte registraci aplikace, kterou jste vytvořili, s oprávněními k rozhraní API pro digitální vlákna Azure s použitím základních oprávnění.

Na stránce portálu pro registraci aplikace vyberte v nabídce *oprávnění rozhraní API* . Na následující stránce oprávnění stiskněte tlačítko *+ Přidat oprávnění* .

:::image type="content" source="media/how-to-set-up-instance/portal/add-permission.png" alt-text="Zobrazení registrace aplikace v Azure Portal, zvýraznění možnosti nabídky oprávnění rozhraní API a tlačítka + Přidat oprávnění":::

Na stránce *oprávnění rozhraní API* , které následuje, přepněte do *rozhraní API moje organizace používá* kartu a vyhledejte *digitální vlákna Azure*. Pokud chcete pokračovat v přiřazování oprávnění pro rozhraní API digitálních vláken Azure, vyberte z výsledků hledání možnost *digitálních vláken Azure* .

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-1.png" alt-text="Zobrazení výsledků hledání stránky žádosti API s informacemi o tom, že se zobrazují digitální vlákna Azure":::

>[!NOTE]
> Pokud máte existující instanci digitálních vláken Azure z předchozí verze Public Preview služby (starší než červenec 2020) ve vašem předplatném, budete muset místo toho hledat *službu inteligentních prostorů Azure* . Toto je starší název pro stejnou sadu rozhraní API a nemění se tím prostředí nad rámec tohoto kroku.

V dalším kroku vyberete, která oprávnění chcete těmto rozhraním API udělit. Rozbalte oprávnění **číst (1)** a zaškrtněte políčko *číst. zapsat* pro udělení oprávnění pro přístup k registraci aplikace a oprávnění k zápisu.

:::image type="content" source="media/how-to-set-up-instance/portal/request-api-permissions-2.png" alt-text="Zobrazení stránky žádosti o oprávnění API výběr oprávnění číst. zapsat pro rozhraní API digitálních vláken Azure":::

Po dokončení stiskněte *Přidat oprávnění* .

### <a name="verify-success"></a>Ověřit úspěch

Zpátky na stránce *oprávnění rozhraní API* ověřte, že je teď záznam pro digitální vlákna Azure, který odráží oprávnění ke čtení a zápisu:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-api-permissions.png" alt-text="Zobrazení portálu oprávnění rozhraní API pro registraci aplikace Azure AD, které zobrazuje přístup pro čtení a zápis pro digitální vlákna Azure":::

Můžete také ověřit připojení k digitálním vyplnění Azure v rámci registrace aplikace *manifest.jsv*, která byla automaticky aktualizována pomocí informací o digitálním prostředí Azure, když jste přidali oprávnění rozhraní API.

Provedete to tak, že v nabídce vyberete *manifest* a zobrazíte kód manifestu registrace aplikace. Posuňte se do dolní části okna Code (kód) a vyhledejte tato pole v části `requiredResourceAccess` . Hodnoty by měly odpovídat hodnotám na snímku obrazovky níže:

:::image type="content" source="media/how-to-set-up-instance/portal/verify-manifest.png" alt-text="Zobrazení portálu manifestu pro registraci aplikace služby Azure AD. Vnořená pod ' requiredResourceAccess ', existuje hodnota ' resourceAppId ' 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 a hodnota ' resourceAccess > ID ' 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

### <a name="collect-important-values"></a>Shromažďovat důležité hodnoty

V dalším kroku na řádku nabídek vyberte *Přehled* a zobrazte podrobnosti o registraci aplikace:

:::image type="content" source="media/how-to-set-up-instance/portal/app-important-values.png" alt-text="Zobrazení důležitých hodnot pro registraci aplikace na portálu":::

Poznamenejte si *ID aplikace (klienta)* a *ID adresáře (tenanta)* **zobrazené na stránce** . Tyto hodnoty budete potřebovat později při [ověřování klientské aplikace proti rozhraním API pro digitální vlákna Azure](how-to-authenticate-client.md). Pokud nejste osoba, která bude psát kód pro takové aplikace, měli byste tyto hodnoty sdílet s osobou, která bude.

### <a name="other-possible-steps-for-your-organization"></a>Další možné kroky pro vaši organizaci

[!INCLUDE [digital-twins-setup-additional-requirements.md](../../includes/digital-twins-setup-additional-requirements.md)]

## <a name="next-steps"></a>Další kroky

Otestujte jednotlivá REST API volání na vaši instanci pomocí příkazů rozhraní příkazového řádku Azure Digital revlákens CLI: 
* [AZ DT reference](https://docs.microsoft.com/cli/azure/ext/azure-iot/dt?view=azure-cli-latest)
* [*Postupy: použití rozhraní příkazového řádku Azure Digital zdvojené*](how-to-use-cli.md)

Případně můžete informace o tom, jak připojit klientskou aplikaci k instanci, napsáním ověřovacího kódu klientské aplikace:
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)