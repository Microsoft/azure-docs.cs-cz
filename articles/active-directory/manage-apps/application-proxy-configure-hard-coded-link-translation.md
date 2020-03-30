---
title: Přeložit odkazy a adresy URL proxy aplikace Azure AD | Dokumenty společnosti Microsoft
description: Popisuje základy konektorů proxy aplikací Azure AD.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69533694"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Přesměrování pevně zakódovaných odkazů pro aplikace publikované pomocí proxy aplikace Azure AD

Azure AD Application Proxy zpřístupňuje místní aplikace uživatelům, kteří jsou vzdálené nebo na svých vlastních zařízeních. Některé aplikace však byly vyvinuty s místními odkazy vloženými do HTML. Tyto odkazy nefungují správně, když je aplikace používána vzdáleně. Pokud máte několik místních aplikací, na které odkazují ostatní, uživatelé očekávají, že odkazy budou pokračovat v práci, když nejsou v kanceláři. 

Nejlepším způsobem, jak zajistit, aby odkazy fungovaly stejně uvnitř i vně podnikové sítě, je nakonfigurovat externí adresy URL vašich aplikací tak, aby byly stejné jako jejich interní adresy URL. Pomocí [vlastních domén](application-proxy-configure-custom-domain.md) nakonfigurujte externí adresy URL tak, aby měly název podnikové domény namísto výchozí domény proxy aplikace.


Pokud nemůžete používat vlastní domény v tenantovi, existuje několik dalších možností pro poskytování této funkce. Všechny tyto jsou také kompatibilní s vlastnídomény a navzájem, takže můžete nakonfigurovat vlastní domény a další řešení v případě potřeby.

> [!NOTE]
> Překlad odkazů není podporován pro pevně zakódované interní adresy URL generované pomocí Javascriptu.

**Možnost 1: Použití spravovaného prohlížeče nebo Microsoft Edge** – Toto řešení je použitelné jenom v případě, že chcete doporučit nebo vyžadovat, aby uživatelé přistupovali k aplikaci prostřednictvím spravovaného prohlížeče Intune nebo prohlížeče Microsoft Edge. Bude zpracovávat všechny publikované adresy URL. 

**Možnost 2: Použijte rozšíření MyApps** – Toto řešení vyžaduje, aby uživatelé nainstalovali rozšíření prohlížeče na straně klienta, ale bude zpracovávat všechny publikované adresy URL a pracuje s nejoblíbenějšími prohlížeči. 

**Možnost 3: Použijte nastavení překladu odkazů** – Jedná se o nastavení admin side, které je pro uživatele neviditelné. Bude však zpracovávat pouze adresy URL v HTML a CSS.   

Tyto tři funkce udržují vaše odkazy v provozu bez ohledu na to, kde jsou vaši uživatelé. Pokud máte aplikace, které odkazují přímo na interní koncové body nebo porty, můžete tyto interní adresy URL namapovat na publikované externí adresy URL proxy aplikací. 

 
> [!NOTE]
> Poslední možnost je jenom pro klienty, kteří z jakéhokoli důvodu nemohou používat vlastní domény k tomu, aby měli pro své aplikace stejné interní a externí adresy URL. Než tuto funkci povolíte, podívejte se, jestli [vlastní domény v proxy aplikací Azure AD můžou](application-proxy-configure-custom-domain.md) pracovat pro vás. 
> 
> Nebo pokud je aplikace, kterou potřebujete nakonfigurovat s překladem odkazů, SharePoint, přečtěte si článek [Konfigurace alternativních mapování přístupu pro SharePoint 2013,](https://technet.microsoft.com/library/cc263208.aspx) kde najdete jiný přístup k mapování odkazů. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>Možnost 1: Intune Managed Browser a Microsoft Edge Integrace 

Pomocí spravovaného prohlížeče Intune nebo Microsoft Edge můžete dále chránit vaši aplikaci a obsah. Chcete-li použít toto řešení, musíte vyžadovat/doporučit uživatelům přístup k aplikaci prostřednictvím spravovaného prohlížeče Intune. Všechny interní adresy URL publikované pomocí proxy aplikace budou spravovaném prohlížečem rozpoznány a přesměrovány na odpovídající externí adresu URL. Tím je zajištěno, že všechny pevně zakódované interní adresy URL fungují, a pokud uživatel přejde do prohlížeče a přímo zadá interní adresu URL, funguje i v případě, že je vzdálený.  

Další informace, včetně konfigurace této možnosti, naleznete v dokumentaci [ke spravovanému prohlížeči.](https://docs.microsoft.com/intune/app-configuration-managed-browser)  

### <a name="option-2-myapps-browser-extension"></a>Možnost 2: Rozšíření prohlížeče MyApps 

S rozšířením prohlížeče MyApps jsou všechny interní adresy URL publikované pomocí proxy aplikace tímto rozšířením rozpoznány a přesměrovány na odpovídající externí adresu URL. Tím je zajištěno, že všechny pevně zakódované interní adresy URL fungují, a pokud uživatel přejde do adresního řádku prohlížeče a přímo zadá interní adresu URL, funguje i v případě, že je vzdálený.  

Chcete-li použít tuto funkci, uživatel musí stáhnout rozšíření a být přihlášen. Pro správce ani uživatele není potřeba žádná jiná konfigurace. 

Další informace, včetně konfigurace této možnosti, naleznete v dokumentaci [k rozšíření prohlížeče MyApps.](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension)

### <a name="option-3-link-translation-setting"></a>Možnost 3: Nastavení překladu odkazů 

Pokud je povolen překlad odkazů, služba Proxy aplikace vyhledá v html a CSS publikované interní odkazy a přeloží je tak, aby uživatelé získali nepřetržité prostředí. Použití rozšíření prohlížeče MyApps je upřednostňováno před nastavením překladu odkazů, protože poskytuje uživatelům výkonnější prostředí.

> [!NOTE]
> Pokud používáte možnost 2 nebo 3, pouze jeden z nich by měl být povolen najednou.

## <a name="how-link-translation-works"></a>Jak funguje překlad odkazů

Po ověření, když proxy server předá data aplikace uživateli, proxy aplikace prohledá aplikaci pro pevně zakódované odkazy a nahradí je příslušnými publikovanými externími adresami URL.

Proxy aplikace předpokládá, že aplikace jsou kódovány v UTF-8. Pokud tomu tak není, zadejte typ kódování v záhlaví `Content-Type:text/html;charset=utf-8`odpovědi http, jako je .

### <a name="which-links-are-affected"></a>Kterých odkazů se to týká?

Funkce překladu odkazů hledá pouze odkazy, které jsou ve značkách kódu v těle aplikace. Proxy aplikace má samostatnou funkci pro překlad souborů cookie nebo adres URL v záhlaví. 

Existují dva běžné typy interních propojení v místních aplikacích:

- **Relativní interní odkazy,** které odkazují na sdílený `/claims/claims.html`prostředek v místní struktuře souborů, jako je . Tyto odkazy automaticky fungují v aplikacích, které jsou publikovány prostřednictvím proxy aplikace a nadále pracovat s překladem odkazů nebo bez něj. 
- **Pevně zakódované interní odkazy** na `http://expenses` jiné místní aplikace, jako jsou publikované soubory, jako je `http://expenses/logo.jpg`. Funkce překladu odkazů funguje na pevně zakódovaných interních odkazech a mění je tak, aby ukazovaly na externí adresy URL, kterými musí vzdálení uživatelé projít.

Úplný seznam tagů kódu HTML, pro které proxy aplikace podporuje překlad odkazů, zahrnuje:
* a
* Audio
* base
* .
* div
* Vložit
* Formulář
* rámec
* Hlavy
* html
* iframe
* obrázek
* vstup
* Odkaz
* Menuitem
* Meta
* objekt
* .
* source
* track
* video

Kromě toho v rámci CSS je také přeložen atribut URL.

### <a name="how-do-apps-link-to-each-other"></a>Jak se aplikace vzájemně propojují?

Překlad odkazů je povolen pro každou aplikaci, takže máte kontrolu nad uživatelským prostředím na úrovni jednotlivých aplikací. Pokud chcete, aby byly přeloženy odkazy *z* této aplikace, ale odkazy *na* tuto aplikaci, zapněte překlad odkazů pro aplikaci. 

Předpokládejme například, že máte tři aplikace publikované prostřednictvím proxy aplikace, které všechny odkazy na sebe navzájem: Výhody, Výdaje a Cestování. Existuje čtvrtá aplikace, Feedback, která není publikována prostřednictvím proxy aplikace.

Pokud povolíte překlad odkazů pro aplikaci Výhody, odkazy na Výdaje a cestování budou přesměrovány na externí adresy URL těchto aplikací, ale odkaz na zpětnou vazbu nebude přesměrován, protože neexistuje žádná externí adresa URL. Odkazy z výdajů a cestování zpět na výhody nefungují, protože překlad odkazů nebyl povolen pro tyto dvě aplikace.

![Odkazy z výhod na jiné aplikace, pokud je povolen překlad odkazů](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Které odkazy nejsou přeloženy?

Chcete-li zvýšit výkon a zabezpečení, některé odkazy nejsou přeloženy:

- Odkazy nejsou uvnitř značek kódu. 
- Odkazy nejsou v HTML nebo CSS. 
- Odkazy ve formátu kódovaném adresou URL.
- Vnitřní odkazy otevřené z jiných programů. Odkazy odeslané prostřednictvím e-mailu nebo rychlé zprávy nebo zahrnuté v jiných dokumentech nebudou přeloženy. Uživatelé potřebují vědět, aby přejděte na externí adresu URL.

Pokud potřebujete podporovat jeden z těchto dvou scénářů, použijte stejné interní a externí adresy URL namísto překladu odkazů.  

## <a name="enable-link-translation"></a>Povolit překlad odkazu

Začínáme s překladem odkazů je stejně snadné jako kliknutí na tlačítko:

1. Přihlaste se k [portálu Azure](https://portal.azure.com) jako správce.
2. Přejděte na**aplikace** >  **Azure Active Directory** > Enterprise**Všechny aplikace** > vybrat aplikaci, kterou chcete spravovat > proxy **aplikace**.
3. Převést **adresy URL v těle aplikace** na **Ano**.

   ![Výběrem možnosti Ano přeložíte adresy URL v těle aplikace.](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Vyberte **Uložit**, aby se tyto změny použily.

Nyní, když vaši uživatelé přístup k této aplikaci, proxy server bude automaticky hledat interní adresy URL, které byly publikovány prostřednictvím proxy aplikace na tenanta.

## <a name="send-feedback"></a>Váš názor

Chceme, aby vaše pomoc fungovala pro všechny vaše aplikace. Prohledáme více než 30 značek v HTML a CSS. Pokud máte příklad generovaných odkazů, které nejsou překládány, odešlete fragment kódu na [zpětnou vazbu proxy aplikace](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Další kroky
[Použití vlastních domén s proxy aplikací Azure AD](application-proxy-configure-custom-domain.md) k mít stejnou interní a externí adresu URL

[Konfigurace alternativních mapování přístupu pro SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
