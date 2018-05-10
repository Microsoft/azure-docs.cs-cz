---
title: Převede uzel odkazy a Proxy aplikace Azure AD adresy URL | Microsoft Docs
description: Popisuje základní informace o Azure AD Application Proxy konektory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e2057241065ecefbbcd398ddf482961359f51de7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Přesměrování pevně zakódované odkazy k aplikacím publikovaným pomocí proxy aplikace služby Azure AD

Proxy aplikace služby Azure AD umožňuje místní aplikace k dispozici pro uživatele, kteří jsou vzdálené nebo na jejich vlastní zařízení. Některé aplikace, ale měla vyvinuté pomocí místních odkazů vložených v kódu HTML. Tyto odkazy správně nefungují, pokud je aplikace používat vzdáleně. Pokud máte několik místní aplikace, přejděte na sobě navzájem, uživatelé očekávají, že na odkazy pokračovat v práci při jejich nejste v kanceláři. 

Ke konfiguraci externí adresy URL aplikací být stejné jako jejich interní adresy URL je nejlepší způsob, jak se ujistěte, že odkazy fungovat stejně uvnitř i vně podnikové sítě. Použití [vlastní domény](active-directory-application-proxy-custom-domains.md) nakonfigurovat tak, aby měl název vaší firemní domény místo výchozí domény proxy aplikace vašeho externí adresy URL.


Pokud nemůžete použít vlastní domény ve vašem klientovi, existuje několik dalších možností pro zajištění tato funkce. Všechny tyto jsou také kompatibilní s vlastní domény a mezi sebou, takže můžete nakonfigurovat vlastní domény a další řešení v případě potřeby. 

**Možnost 1: Použití ve spravovaném prohlížeči** – toto řešení je k dispozici jenom Pokud máte v úmyslu doporučujeme nebo vyžadovat, aby uživatelé přistupovat k aplikaci prostřednictvím Intune Managed Browser. Zpracuje všechny publikované adresy URL. 

**Možnost 2: Použití rozšíření MyApps** – toto řešení vyžaduje, aby uživatelé nainstalovat rozšíření prohlížeče na straně klienta, ale zpracuje všechny publikované adresy URL a spolupracuje s většinou oblíbených prohlížeče. 

**Možnost 3: Použijte nastavení překladu odkaz** – to je nastavení straně správce, který je pro uživatele skrytá. Však bude zpracovávat jenom adresy URL v kódu HTML a CSS. Pevně interní adresy URL vygenerované pomocí jazyka Javascript (například) nebude fungovat.  

Tyto tři funkce zachovat vaše odkazy práce bez ohledu na to, kde jsou vaši uživatelé. Pokud máte aplikace, které přejděte přímo na vnitřních koncových bodů nebo porty, můžete namapovat tyto interní adresy URL publikované externí URL Proxy aplikace. 

 
> [!NOTE]
> Poslední možnost je jenom pro klienty, kteří ať důvodu, nelze použít vlastní domény tak, aby měl stejné interní a externí adresy URL pro svoje aplikace. Před povolením této funkce najdete v části Pokud [vlastních domén v Azure AD Application Proxy](active-directory-application-proxy-custom-domains.md) může fungovat pro vás. 

>Nebo, pokud je aplikace, budete muset nakonfigurovat s odkazem překlad služby SharePoint, najdete v části [konfigurace mapování alternativních adres URL pro službu SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) jiný způsob mapování odkazy. 

 
### <a name="option-1-intune-managed-browser-integration"></a>Možnost 1: Intune Managed Browser integrace 

Dalším stupněm ochrany vaší aplikace a obsah, můžete použít v prohlížeči spravovaném Intune. Pokud chcete používat toto řešení, budete muset vyžadují/doporučujeme uživatelům přístup aplikace prostřednictvím Intune Managed Browser. Všechny interní adresy URL, které jsou publikované pomocí Proxy aplikace bude rozpoznáno spravovaný prohlížeč a přesměruje na odpovídající externí adresu URL. To zajišťuje fungovat všechny pevně interní adresy URL, a když uživatel přejde do prohlížeče a přímo typy interní adresa URL, funguje i v případě, že uživatel je vzdálený.  

Další informace, včetně toho, jak nakonfigurovat tuto možnost najdete [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) dokumentaci.  

### <a name="option-2-myapps-browser-extension"></a>Možnost 2: Rozšíření prohlížeče MyApps 

S příponou prohlížeče MyApps všechny interní adresy URL, které jsou publikované pomocí Proxy aplikace rozpoznány rozšíření a přesměrování na odpovídající externí adresu URL. To zajišťuje fungovat všechny pevně interní adresy URL, a když uživatel přejde na panelu Adresa prohlížeče a přímo typy interní adresa URL, funguje i v případě, že uživatel je vzdálený.  

Uživatel musí pro tuto funkci používat, stáhněte si rozšíření a být přihlášení. Není potřeba správci a uživatelé žádná další konfigurace. 

 

### <a name="option-3-link-translation-setting"></a>Možnost 3: Nastavení překladu odkaz 

Pokud je povoleno překlad odkaz, služba Proxy aplikace prostřednictvím HTML a CSS hledá publikované interní odkazy a přeloží je tak, aby uživatelé získají bez přerušení prostředí. 



## <a name="how-link-translation-works"></a>Jak funguje překlad propojení

Po ověření pokud proxy server předává data aplikací pro uživatele, Proxy aplikace vyhledá aplikace pro pevně zakódované odkazy a nahradí je jejich odpovídajících publikovaná externí adresy URL.

Proxy aplikace se předpokládá, že aplikace jsou kódování ve formátu UTF-8. Pokud není tento případ, zadejte typ kódování v hlavičce http odpovědi jako `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Odkazy, které se vztahuje?

Funkci překlad odkazů pouze vyhledá odkazy, které jsou v kódu značek v těle aplikace. Proxy aplikace má samostatné funkce pro překlad adres URL nebo soubory cookie v záhlaví. 

Existují dva typy běžné interní odkazů v místním aplikacím:

- **Relativní odkazy interní** , přejděte na příkaz sdíleného prostředku do místního souboru struktury jako `/claims/claims.html`. Tyto odkazy se automaticky fungovat v aplikacích, které jsou publikované prostřednictvím Proxy aplikace a pokračovat v práci s nebo bez překladu odkaz. 
- **Vnitřní propojení pevně zakódované** do jiných aplikací místní jako `http://expenses` nebo publikovat soubory, jako jsou `http://expenses/logo.jpg`. Funkci překlad odkazů na interní odkazy pevně zakódované funguje a je tak, aby odkazoval na externí adresy URL, které je třeba projít vzdálení uživatelé změní.

### <a name="how-do-apps-link-to-each-other"></a>Jak aplikace propojit k sobě navzájem?

Překlad odkaz je povolena pro každou aplikaci tak, že budete mít kontrolu nad činnost koncového uživatele na úrovni pro aplikaci. Zapnout překlad odkaz pro aplikaci, když chcete, aby odkazy *z* aplikaci k převodu, není odkazy *k* tuto aplikaci. 

Předpokládejme například, že máte tři aplikacích publikovaných prostřednictvím Proxy aplikace všech propojení mezi sebou: výhody, náklady a cesta. Je čtvrtá aplikaci a zpětnou vazbu, která není publikována prostřednictvím Proxy aplikace.

Když povolíte překlad odkaz pro aplikaci výhody, odkazy na výdaje a cesta přesměrování na externí adresy URL pro tyto aplikace, ale odkaz zpětná vazba není přesměrován, protože neexistuje žádná externí adresa URL. Nefungují odkazy z výdajů a cesta zpět na výhody, protože odkaz překlad nebyla povolena pro tyto dvě aplikace.

![Odkazy z výhody do jiných aplikací, pokud je povoleno překlad odkaz](./media/application-proxy-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Odkazy, které nejsou přeloženy?

Pro zvýšení výkonu a zabezpečení, nejsou přeložit některé odkazy:

- Odkazy není uvnitř značky kódu. 
- Odkazy není v HTML nebo šablon stylů CSS. 
- Vnitřní propojení otevřít z jiných aplikací. Odkazy budou odesílat prostřednictvím e-mailu nebo pomocí rychlé zprávy nebo součástí jiné dokumenty, nebude možné přeložit. Uživatelé musí vědět, přejít na externí adresu URL.

Pokud potřebujete podporovat některý z těchto dvou scénářů, použijte místo odkaz překlad stejné interní a externí adresy URL.  

## <a name="enable-link-translation"></a>Povolit překlad odkaz

Začínáme s překlad odkaz je stejně snadná jako kliknutí na tlačítko:

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **podnikové aplikace, které** > **všechny aplikace** > vyberte aplikaci, kterou chcete spravovat > **proxy aplikace**.
3. Zapnout **překládat adresy URL v textu aplikace** k **Ano**.

   ![Vyberte možnost Ano překládat adresy URL v textu aplikace](./media/application-proxy-link-translation/select_yes.png).
4. Vyberte **Uložit** proveďte změny.

Teď když vaši uživatelé přístup k této aplikaci, proxy server automaticky vyhledá interní adresy URL, které byly publikovány prostřednictvím Proxy aplikace na klientovi.

## <a name="send-feedback"></a>Odeslat názor

Chceme, abyste Ujistěte se, tato funkce fungovat pro všechny aplikace. Jsme hledání více než 30 značky v kódu HTML a CSS. Pokud máte příkladem generovaného odkazy, které nejsou se překlad vztahuje, pošlete fragment kódu do [zpětnou vazbu Proxy aplikací](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Další postup
[Použití vlastních domén s Azure AD Application Proxy](active-directory-application-proxy-custom-domains.md) mít stejnou interní a externí adresu URL

[Konfigurace mapování alternativních adres URL pro službu SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
