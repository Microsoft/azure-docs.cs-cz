---
title: Vytvoření registrace aplikace
titleSuffix: Azure Digital Twins
description: Podívejte se, jak vytvořit registraci aplikace služby Azure AD, jako možnost ověřování pro klientské aplikace.
author: baanders
ms.author: baanders
ms.date: 10/13/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: acb5457f10c54a741a738dd8a1008e703b0f23b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102051017"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins"></a>Vytvoření registrace aplikace, která se má použít u digitálních vláken Azure

Při práci s instancí digitálních vláken Azure je běžné pracovat s touto instancí prostřednictvím klientských aplikací, jako je například vlastní klientská aplikace nebo ukázka, jako je [Průzkumník digitálních vláken Azure](quickstart-adt-explorer.md). Tyto aplikace se musí ověřit u digitálních vláken Azure, aby s ní mohli pracovat, a některé [mechanismy ověřování](how-to-authenticate-client.md) , které můžou aplikace použít, zahrnují **registraci aplikace** [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) .

To není vyžadováno pro všechny scénáře ověřování. Pokud ale používáte strategii ověřování nebo ukázku kódu, která vyžaduje registraci aplikace, včetně **ID klienta** a **ID tenanta**, Tento článek popisuje, jak ho nastavit.

## <a name="using-azure-ad-app-registrations"></a>Používání registrací aplikací Azure AD

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) je cloudová služba pro správu identit a přístupu od Microsoftu. Nastavení **Registrace aplikace** v Azure AD je jedním ze způsobů, jak udělit klientské aplikaci přístup k digitálním vlákenám Azure.

V této registraci aplikace nakonfigurujete přístupová oprávnění k [rozhraním API pro digitální vlákna Azure](how-to-use-apis-sdks.md). Později se klientské aplikace můžou ověřit proti registraci aplikace s využitím **hodnot ID klienta a ID tenanta** a v důsledku toho se jim přidělí nakonfigurovaná přístupová oprávnění k rozhraním API.

>[!TIP]
> Můžete chtít nastavit novou registraci aplikace pokaždé, když ji potřebujete, *nebo* to udělat jenom jednou a vytvořit jedinou registraci aplikace, která se bude sdílet mezi všemi scénáři, které to vyžadují.

## <a name="create-the-registration"></a>Vytvořit registraci

Začněte tím, že přejdete na [Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) v Azure Portal (můžete použít tento odkaz nebo ho najít pomocí panelu hledání na portálu). V nabídce služba vyberte *Registrace aplikací* a potom klikněte na *+ Nová registrace*.

:::image type="content" source="media/how-to-create-app-registration/new-registration.png" alt-text="Zobrazení stránky služby Azure AD v Azure Portal, zvýraznění možnosti nabídky Registrace aplikací a + nová registrace":::

Na následující stránce *Registrovat aplikaci* vyplňte požadované hodnoty:
* **Název**: zobrazovaný název aplikace Azure AD, který se má přidružit k registraci.
* **Podporované typy účtů**: vyberte *účty jenom v tomto organizačním adresáři (jenom výchozí adresář – jeden tenant)* .
* **Identifikátor URI pro přesměrování**: *Adresa URL pro odpověď aplikace Azure AD* pro aplikaci Azure AD. Přidejte identifikátor URI *veřejného klienta/nativního (mobilní & Desktop)* pro `http://localhost` .

Až skončíte, stiskněte tlačítko *zaregistrovat* .

:::image type="content" source="media/how-to-create-app-registration/register-an-application.png" alt-text="Zobrazení stránky registrace aplikace s popsanými hodnotami, které jsou vyplněny":::

Po dokončení nastavení registrace bude portál přesměrován na stránku podrobností.

## <a name="collect-client-id-and-tenant-id"></a>Shromáždit ID klienta a ID tenanta

Dále Shromážděte některé důležité hodnoty o registraci aplikace na stránce s jejími podrobnostmi:

:::image type="content" source="media/how-to-create-app-registration/app-important-values.png" alt-text="Zobrazení důležitých hodnot pro registraci aplikace na portálu":::

Poznamenejte si _**ID aplikace (klienta)**_ a _**ID adresáře (tenanta)**_ **zobrazené na stránce** . Jedná se o hodnoty, které klientská aplikace bude potřebovat k ověření pomocí digitálních vláken Azure pomocí této registrace.

## <a name="provide-azure-digital-twins-api-permission"></a>Poskytnutí oprávnění rozhraní API pro digitální vlákna Azure

V dalším kroku nakonfigurujte registraci aplikace, kterou jste vytvořili, s oprávněními k rozhraní API pro digitální vlákna Azure s použitím základních oprávnění.

Na stránce portálu pro registraci aplikace vyberte v nabídce *oprávnění rozhraní API* . Na následující stránce oprávnění stiskněte tlačítko *+ Přidat oprávnění* .

:::image type="content" source="media/how-to-create-app-registration/add-permission.png" alt-text="Zobrazení registrace aplikace v Azure Portal, zvýraznění možnosti nabídky oprávnění rozhraní API a tlačítka + Přidat oprávnění":::

Na stránce *oprávnění rozhraní API* , které následuje, přepněte do *rozhraní API moje organizace používá* kartu a vyhledejte *digitální vlákna Azure*. Pokud chcete pokračovat v přiřazování oprávnění pro rozhraní API digitálních vláken Azure, vyberte z výsledků hledání možnost _**digitálních vláken Azure**_ .

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1.png" alt-text="Podívejte se na výsledek hledání stránky žádosti rozhraní API s informacemi o tom, že se jedná o digitální vlákna Azure s ID aplikace (klienta) typu 0b07f429-9f4b-4714-9392-cc5e8e80c8b0.":::

>[!NOTE]
> Pokud vaše předplatné stále obsahuje existující instanci digitálních vláken Azure z předchozí verze Public Preview služby (do července 2020), budete muset místo toho vyhledat a vybrat _**službu Azure Smart Spaces**_ . Toto je starší název pro stejnou sadu rozhraní API (Všimněte si, že *ID aplikace (klienta)* je stejné jako na snímku obrazovky výše) a vaše prostředí se po tomto kroku nemění.
> :::image type="content" source="media/how-to-create-app-registration/request-api-permissions-1-smart-spaces.png" alt-text="Zobrazení výsledku hledání stránky s oprávněními rozhraní API, které zobrazuje službu inteligentních prostorů Azure":::

V dalším kroku vyberete, která oprávnění chcete těmto rozhraním API udělit. Rozbalte oprávnění **číst (1)** a zaškrtněte políčko *číst. zapsat* pro udělení oprávnění pro přístup k registraci aplikace a oprávnění k zápisu.

:::image type="content" source="media/how-to-create-app-registration/request-api-permissions-2.png" alt-text="Zobrazení stránky žádosti o oprávnění API výběr oprávnění číst. zapsat pro rozhraní API digitálních vláken Azure":::

Po dokončení stiskněte *Přidat oprávnění* .

### <a name="verify-success"></a>Ověřit úspěch

Na stránce *oprávnění rozhraní API* ověřte, že je teď položka pro digitální vlákna Azure, která odráží oprávnění ke čtení a zápisu:

:::image type="content" source="media/how-to-create-app-registration/verify-api-permissions.png" alt-text="Zobrazení portálu oprávnění rozhraní API pro registraci aplikace Azure AD, které zobrazuje přístup pro čtení a zápis pro digitální vlákna Azure":::

Můžete také ověřit připojení k digitálním vyplnění Azure v rámci registrace aplikace *manifest.jsv*, která byla automaticky aktualizována pomocí informací o digitálním prostředí Azure, když jste přidali oprávnění rozhraní API.

Provedete to tak, že v nabídce vyberete *manifest* a zobrazíte kód manifestu registrace aplikace. Posuňte se do dolní části okna Code (kód) a vyhledejte tato pole v části `requiredResourceAccess` . Hodnoty by měly odpovídat hodnotám na snímku obrazovky níže:

:::image type="content" source="media/how-to-create-app-registration/verify-manifest.png" alt-text="Zobrazení portálu manifestu pro registraci aplikace služby Azure AD. Vnořená pod ' requiredResourceAccess ', existuje hodnota ' resourceAppId ' 0b07f429-9f4b-4714-9392-cc5e8e80c8b0 a hodnota ' resourceAccess > ID ' 4589bd03-58cb-4e6c-b17f-b580e39652f8":::

Pokud tyto hodnoty chybí, opakujte postup v [části pro přidání oprávnění rozhraní API](#provide-azure-digital-twins-api-permission).

## <a name="other-possible-steps-for-your-organization"></a>Další možné kroky pro vaši organizaci

Je možné, že vaše organizace vyžaduje další akce od vlastníků nebo správců předplatného k úspěšnému nastavení registrace aplikace. Požadované kroky se mohou lišit v závislosti na konkrétním nastavení vaší organizace.

Tady jsou některé běžné potenciální aktivity, které bude muset vlastník nebo správce předplatného provést. Tyto a jiné operace lze provést ze stránky [*aplikace Azure AD registrace*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) v Azure Portal.
* Udělte souhlas správce s registrací aplikace. Pro všechny registrace aplikací v rámci vašeho předplatného může být ve službě Azure AD ve vaší organizaci globálně zapnutý *souhlas správce* . Pokud ano, bude muset vlastník nebo správce vybrat toto tlačítko pro vaši společnost na stránce *oprávnění rozhraní API* pro registraci aplikace, aby byla platná pro registraci aplikace:

    :::image type="content" source="media/how-to-create-app-registration/grant-admin-consent.png" alt-text="Zobrazení portálu pro tlačítko udělení souhlasu správce v části oprávnění rozhraní API":::
  - Pokud byl souhlas udělen úspěšně, měla by položka pro digitální vlákna Azure zobrazit hodnotu *stavu* _udělenou pro **(vaše společnost)**_ .
   
    :::image type="content" source="media/how-to-create-app-registration/granted-admin-consent-done.png" alt-text="Zobrazení portálu souhlasu správce udělené pro společnost pod oprávněními rozhraní API":::
* Aktivace přístupu veřejného klienta
* Nastavit konkrétní adresy URL odpovědí pro přístup k webu a desktopu
* Povolení pro toky implicitního ověřování OAuth2

Další informace o registraci aplikace a jejích různých možnostech instalace najdete v tématu [*Registrace aplikace s platformou Microsoft Identity*](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Další kroky

V tomto článku nastavíte registraci aplikace služby Azure AD, která se dá použít k ověřování klientských aplikací pomocí rozhraní API digitálních vláken Azure.

Dále si přečtěte informace o ověřovacích mechanismech, včetně těch, které používají registrace aplikací a jiné, které nezahrnují:
* [*Postupy: psaní kódu ověřování aplikace*](how-to-authenticate-client.md)