---
title: Přeložit odkazy a adresy URL Aplikace Azure AD proxy | Microsoft Docs
description: Zahrnuje základní informace o konektorech Azure Proxy aplikací služby AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa0dc2081aff5a24fb830b756131cccd5c6ce810
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "69533694"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Přesměrování pevně zakódované odkazů pro aplikace publikované pomocí Azure Proxy aplikací služby AD

Azure Proxy aplikací služby AD zpřístupňuje vaše místní aplikace uživatelům, kteří jsou na vzdálených nebo na svých zařízeních. Některé aplikace se ale vyvinuly s místními odkazy, které jsou vložené v HTML. Tyto odkazy nefungují správně, když se aplikace používá vzdáleně. Když máte několik místních aplikací navzájem, je třeba, že uživatelé budou mít odkazy na práci, i když nejsou v kanceláři. 

Nejlepším způsobem, jak zajistit, aby odkazy fungovaly v rámci podnikové sítě i mimo ni, je nakonfigurovat externí adresy URL vašich aplikací tak, aby byly stejné jako jejich interní adresy URL. Pomocí [vlastních domén](application-proxy-configure-custom-domain.md) můžete nakonfigurovat externí adresy URL tak, aby místo výchozí domény proxy aplikace používaly název vaší podnikové domény.


Pokud ve vašem tenantovi nemůžete použít vlastní domény, existuje několik dalších možností, jak tuto funkci poskytnout. Všechny tyto jsou také kompatibilní s vlastními doménami a navzájem, takže v případě potřeby můžete nakonfigurovat vlastní domény a další řešení.

> [!NOTE]
> Překlad propojení není podporován pro pevně zakódované interní adresy URL vygenerované prostřednictvím JavaScriptu.

**Možnost 1: použijte Managed Browser nebo Microsoft Edge** – toto řešení je možné použít pouze v případě, že plánujete doporučit nebo vyžadovat, aby uživatelé měli přístup k aplikaci přes Intune Managed Browser nebo prohlížeč Microsoft Edge. Zpracuje všechny publikované adresy URL. 

**Možnost 2: použití rozšíření MyApp** – toto řešení vyžaduje, aby uživatelé nainstalovali rozšíření prohlížeče na straně klienta, ale budou zpracovávat všechny publikované adresy URL a fungují s nejoblíbenějšími prohlížeči. 

**Možnost 3: použití nastavení překladu propojení** – Toto je nastavení na straně správce, které je pro uživatele neviditelné. Zpracuje ale jenom adresy URL v HTML a CSS.   

Díky těmto třem funkcím budou vaše odkazy fungovat bez ohledu na to, kde jsou vaši uživatelé. Pokud máte aplikace, které přímo odkazují na interní koncové body nebo porty, můžete tyto interní adresy URL namapovat na publikované adresy URL proxy externích aplikací. 

 
> [!NOTE]
> Poslední možnost je jenom pro klienty, které z jakéhokoli důvodu nemůžou používat vlastní domény, aby měly stejné interní a externí adresy URL pro své aplikace. Než tuto funkci povolíte, přečtěte si článek o tom, jestli můžou [vlastní domény v Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md) fungovat za vás. 
> 
> Nebo pokud je aplikace, kterou potřebujete nakonfigurovat s překladem odkazů, SharePoint, přečtěte si téma [Konfigurace mapování alternativních adres pro SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) pro další přístup k mapování odkazů. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>Možnost 1: Intune Managed Browser a Microsoft Edge Integration 

K další ochraně aplikace a obsahu můžete použít Intune Managed Browser nebo Microsoft Edge. Chcete-li použít toto řešení, je nutné vyžadovat nebo doporučit uživatelům přístup k aplikaci prostřednictvím Intune Managed Browser. Všechny interní adresy URL publikované s proxy aplikací budou rozpoznány Managed Browser a přesměrovány na odpovídající externí adresu URL. Tím se zajistí, že všechny pevně zakódované interní adresy URL fungují a když uživatel přejde do prohlížeče a přímo zadá interní adresu URL, funguje i v případě, že je uživatel vzdálený.  

Další informace, včetně postupu konfigurace této možnosti, najdete v dokumentaci k [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) .  

### <a name="option-2-myapps-browser-extension"></a>Možnost 2: rozšíření prohlížeče aplikace Mojeapl 

V rozšíření prohlížeče aplikace MyApp jsou všechny interní adresy URL publikované s proxy aplikací rozpoznány rozšířením a přesměrovány na odpovídající externí adresu URL. Tím se zajistí, že všechny pevně zakódované interní adresy URL fungují a když uživatel přejde na adresní řádek prohlížeče a přímo zadá interní adresu URL, funguje i v případě, že je uživatel vzdálený.  

Aby bylo možné tuto funkci používat, musí si uživatel stáhnout rozšíření a přihlásit se. Pro správce nebo uživatele není nutná žádná další konfigurace. 

Další informace, včetně postupu konfigurace této možnosti, najdete v dokumentaci k [rozšíření prohlížeče MyApp](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension) .

### <a name="option-3-link-translation-setting"></a>Možnost 3: nastavení překladu propojení 

Pokud je povolen překlad propojení, služba proxy aplikací prohledává publikované interní odkazy pomocí HTML a šablon stylů CSS a překládá je, aby uživatelé získali nepřerušované prostředí. Použití rozšíření prohlížeče MyApps je upřednostňováno s nastavením překladu propojení, protože poskytuje uživatelům lepší možnosti.

> [!NOTE]
> Pokud používáte možnost 2 nebo 3, měla by být povolena pouze jedna z těchto možností.

## <a name="how-link-translation-works"></a>Jak funguje Překlad propojení

Po ověření, když proxy server předá data aplikace uživateli, proxy aplikace prohledá aplikaci pro odkazy pevně zakódované a nahradí je jejich příslušnými, publikovanými externími adresami URL.

Proxy aplikací předpokládá, že jsou aplikace kódované v kódování UTF-8. V takovém případě zadejte typ kódování v hlavičce odpovědi HTTP, například `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Které odkazy jsou ovlivněny?

Funkce překladu odkazů hledá pouze odkazy, které jsou v těle aplikace. Proxy aplikace má samostatnou funkci pro překládání souborů cookie a adres URL v hlavičkách. 

Existují dva běžné typy interních propojení v místních aplikacích:

- **Relativní interní odkazy** , které odkazují na sdílený prostředek v místní struktuře souborů, jako `/claims/claims.html`je. Tyto odkazy automaticky fungují v aplikacích, které jsou publikovány prostřednictvím proxy aplikace, a fungují i bez překladu propojení. 
- **Pevně zakódované interní odkazy** na jiné místní aplikace, jako jsou `http://expenses` nebo publikované soubory `http://expenses/logo.jpg`. Funkce překladu propojení funguje na interních odkazech pevně zakódované a mění je tak, aby odkazovaly na externí adresy URL, ke kterým musí přejít vzdálení uživatelé.

Úplný seznam značek kódu HTML, na kterých proxy aplikací podporuje překlad odkazů pro zahrnutí:
* a
* kazet
* base
* .
* div
* Uložit
* tvar
* rámec
* záhlaví
* html
* iframe
* obrázek
* vstup
* propojit
* MenuItem
* meta
* objekt
* .
* source
* track
* video

Kromě toho je v rámci šablony stylů CSS také přeložen atribut URL.

### <a name="how-do-apps-link-to-each-other"></a>Jak aplikace vzájemně odkazují?

Překlad propojení je povolený pro každou aplikaci, takže budete mít kontrolu nad uživatelským prostředím na úrovni jednotlivých aplikací. Pokud chcete, aby byly odkazy *z* této aplikace přeloženy, neodkazujte *na* tuto aplikaci, zapněte Překlad propojení pro aplikaci. 

Předpokládejme například, že máte tři aplikace publikované prostřednictvím proxy aplikací, které všechny vzájemně odkazují: výhody, výdaje a cestování. Máte čtvrtou aplikaci, zpětnou vazbu, která není publikovaná prostřednictvím proxy aplikací.

Pokud povolíte Překlad propojení pro aplikaci s výhodami, odkazy na výdaje a cestování budou přesměrovány na externí adresy URL těchto aplikací, ale odkaz na zpětnou vazbu nebude přesměrován, protože není k dispozici externí adresa URL. Odkazy z výdajů a cestování zpátky na výhody nefungují, protože pro tyto dvě aplikace nebyl povolen překlad propojení.

![Odkazy z výhod na jiné aplikace v případě, že je povolený Překlad propojení](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Které odkazy nejsou přeloženy?

Pro zvýšení výkonu a zabezpečení nejsou některé odkazy přeloženy:

- Odkazy nejsou uvnitř značek kódu. 
- Odkazy, které nejsou v HTML nebo CSS. 
- Odkazy ve formátu kódovaném adresou URL.
- Interní odkazy otevřené z jiných programů. Odkazy odeslané prostřednictvím e-mailu nebo rychlé zprávy nebo zahrnuté do jiných dokumentů nebudou přeloženy. Uživatelé potřebují znát, aby mohli přejít na externí adresu URL.

Pokud potřebujete podporovat jeden z těchto dvou scénářů, použijte místo překladu odkazů stejné interní a externí adresy URL.  

## <a name="enable-link-translation"></a>Povolit překlad propojení

Začínáme s překladem odkazů je stejně snadné jako kliknutí na tlačítko:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako správce.
2. Přejít na **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace** > vyberte aplikaci, kterou chcete spravovat > **aplikační proxy**.
3. **V části text aplikace převeďte adresy URL pro převod** na **Ano**.

   ![Vyberte Ano, pokud chcete překládat adresy URL v těle aplikace.](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Vyberte **Uložit**, aby se tyto změny použily.

Když teď uživatel přistupuje k této aplikaci, proxy automaticky vyhledá interní adresy URL, které se publikovaly prostřednictvím proxy aplikací ve vašem tenantovi.

## <a name="send-feedback"></a>Odeslat názor

Chceme, aby tato funkce fungovala pro všechny vaše aplikace. Hledáme více než 30 značek v HTML a CSS. Pokud máte příklad vygenerovaných odkazů, které nejsou přeloženy, odešlete fragment kódu na [zpětnou vazbu k proxy aplikace](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Další kroky
[Použití vlastních domén s Azure proxy aplikací služby AD](application-proxy-configure-custom-domain.md) ke stejné interní a externí adrese URL

[Konfigurace mapování alternativního přístupu pro SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
