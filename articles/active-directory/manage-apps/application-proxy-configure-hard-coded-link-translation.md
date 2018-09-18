---
title: Přeložit odkazy a adresy URL Azure AD App Proxy | Dokumentace Microsoftu
description: Popisuje základní informace o konektory Proxy aplikací Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/04/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 0d139437a069406db2ae0f189480072f8063a327
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "45982268"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Přesměrovat odkazy pevně zakódované k aplikacím publikovaným pomocí Proxy aplikací Azure AD

Azure AD Application Proxy zpřístupňuje místních aplikací, uživatelé, kteří jsou vzdálené nebo na vlastních zařízeních. Některé aplikace, ale byly vyvinuty s místních odkazů vložený v kódu HTML. Tyto odkazy nefungují správně Pokud aplikace používá vzdáleně. Až budete mít několik místních aplikací, přejděte k sobě navzájem, uživatelé očekávají, že odkazy na pracovat dál, pokud nejsou v kanceláři. 

Ke konfiguraci externí adresy URL vaší aplikace bude stejná jako jejich interní adresy URL je nejlepší způsob, jak Ujistěte se, že odkazy fungují stejně uvnitř i mimo firemní síť. Použití [vlastních domén](application-proxy-configure-custom-domain.md) ke konfiguraci vaší externí adresy URL mít název vaší firemní domény místo výchozí domény aplikace proxy serveru.


Pokud nemůžete použít vlastní domény ve vašem tenantovi, existuje několik dalších možností pro poskytnutí této funkce. Všechny tyto budou také kompatibilní s vlastní domény a mezi sebou, takže můžete nakonfigurovat vlastní domény a dalších řešení v případě potřeby. 

**Možnost 1: Použití v aplikaci Managed Browser** – toto řešení platí pouze pokud budete chtít doporučujeme nebo vyžadovat, aby uživatelé přístup k aplikaci přes Intune Managed Browser. Zpracuje všechny publikované adresy URL. 

**Možnost 2: Použití rozšíření MyApps** – toto řešení vyžaduje, aby uživatelé nainstalovat rozšíření na straně klienta prohlížeče, ale bude zpracovávat všechny publikované adresy URL a spolupracuje s většinou oblíbených prohlížeče. 

**Možnost 3: Použití nastavení překladu odkaz** – Toto je nastavení na straně správce, který není viditelný pro uživatele. Však bude zpracovávat jenom adresy URL v kódu HTML a CSS. Pevně zakódované interní adresy URL vygenerované pomocí jazyka Javascript (například) nebudou fungovat.  

Tyto tři funkce zachovat propojení práce bez ohledu na to, kde jsou vaši uživatelé. Pokud máte aplikace, které přejděte přímo na koncovým bodům s interním nebo porty, můžete namapovat tyto vnitřní adresy URL na publikované externí aplikace Proxy adresy URL. 

 
> [!NOTE]
> Poslední možnost je pouze pro klienty, kteří pro všechno, co z důvodu, nelze použít vlastní domény k mají stejné interní a externí adresy URL pro svoje aplikace. Před povolením této funkce najdete v článku Pokud [vlastních domén v Azure AD Application Proxy](application-proxy-configure-custom-domain.md) může pro vás nejvhodnější. 

>Nebo, pokud je aplikace, je potřeba nakonfigurovat s odkazem na překlad služby SharePoint, naleznete v tématu [konfigurace alternativních mapování přístupu pro SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) jiný způsob mapování odkazy. 

 
### <a name="option-1-intune-managed-browser-integration"></a>Možnost 1: Spravovaná pomocí Intune Integration prohlížeče 

Intune Managed Browser můžete rozšířit ochranu, aplikace a obsah. Pokud chcete používat toto řešení, budete muset vyžadují/doporučujeme uživatelům přístup k aplikaci prostřednictvím Intune Managed Browser. Všechny interní adresy URL, které jsou publikované pomocí Proxy aplikace služby bude v aplikaci Managed Browser rozpozná a přesměruje na odpovídající externí adresu URL. Tím se zajistí, že všechny pevně zakódované interní adresy URL pracovat, a pokud uživatel přejde v prohlížeči a přímo typy interní adresa URL, to funguje i v případě, že uživatel je vzdálený.  

Další informace, včetně postupu konfigurace této možnosti najdete v tématu [Managed Browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) dokumentaci.  

### <a name="option-2-myapps-browser-extension"></a>Možnost 2: Rozšíření prohlížeče MyApps 

Pomocí rozšíření prohlížeče MyApps všechny interní adresy URL, které jsou publikované pomocí Proxy aplikace rozpozná rozšíření a přesměrování na odpovídající externí adresu URL. Tím se zajistí, že všechny pevně zakódované interní adresy URL pracovat, a pokud uživatel přejde do adresního řádku prohlížeče a přímo typy interní adresa URL, to funguje i v případě, že uživatel je vzdálený.  

Uživatel musí na tuto funkci použít, stáhněte si rozšíření a být přihlášení. Není pro správci a uživatelé nepotřebují žádnou další konfiguraci. 

 

### <a name="option-3-link-translation-setting"></a>Možnost 3: Nastavení překladu odkaz 

Při překladu odkazu je povoleno, Proxy aplikací služby pomocí HTML a CSS hledá publikované vnitřní propojení a převede je tak, aby uživatelé získají prostředí bez přerušení. 



## <a name="how-link-translation-works"></a>Jak propojit funguje překlad

Po ověření když proxy server předává data aplikací na uživatele, Proxy aplikace vyhledá aplikace pevně zakódované odkazy a nahradí je jejich odpovídajících publikovat externí adresy URL.

Proxy aplikací se předpokládá, že aplikace jsou kódovány ve formátu UTF-8. Pokud to není tento případ, zadejte typ kódování v hlavičce odpovědi protokolu http, jako je třeba `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Které odkazy se to týká?

Překlad odkazů pouze hledá odkazy, které jsou v značky kódu v těle aplikace. Proxy aplikace má samostatné funkce pro převod souborů cookie nebo adresy URL v hlavičkách. 

Existují dva běžné typy interní odkazů v místním aplikacím:

- **Relativní odkazy interní** , že bod ke sdíleným prostředkům ve struktuře místního souboru jako `/claims/claims.html`. Tyto odkazy mělo automaticky fungovat v aplikacích, které jsou publikované prostřednictvím Proxy aplikací a pokračovat v práci s nebo bez překladu odkazu. 
- **Vnitřní propojení pevně zakódované** do dalších místních aplikací, jako je `http://expenses` nebo publikované soubory jako `http://expenses/logo.jpg`. Překlad odkazů funguje na odkazech interní pevně zakódované a změní, je tak, aby odkazoval na externí adresy URL, které je třeba projít vzdálení uživatelé.

### <a name="how-do-apps-link-to-each-other"></a>Jak se navzájem propojují aplikace?

Překlad odkazů je povolená pro každou aplikaci tak, že budete mít kontrolu nad činnost koncového uživatele na úrovni aplikace za. Zapnout překlad odkazu pro aplikace, pokud chcete odkazy *z* tuto aplikaci k převodu, nikoli odkazy *k* tuto aplikaci. 

Předpokládejme například, že budete mít tři aplikace publikované prostřednictvím Proxy aplikací, které jsou všechny se navzájem propojují: výhod, nákladů a cesty. Je čtvrtý aplikace, zpětnou vazbu, která není publikované prostřednictvím Proxy aplikací.

Když povolíte překlad odkazu pro aplikaci výhody, odkazy na výdaje a cestovní ruch přesměrování na externí adresy URL pro tyto aplikace, ale na odkaz pro zpětnou vazbu není přesměrovat, protože neexistuje žádná externí adresa URL. Odkazy z výdaje a cestovní ruch zpět na výhody nefungují, protože odkaz překladu nebyl povolen pro tyto dvě aplikace.

![Odkazy na základě výhod do jiných aplikací, když je povolen překlad odkazu](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Odkazů je nepřekládá?

Pokud chcete zlepšit výkon a zabezpečení, nepřekládá některé odkazy:

- Odkazy nejsou uvnitř značky kódu. 
- Propojení není ve formátu HTML nebo šablon stylů CSS. 
- Otevřít interní odkazy z jiných aplikací. Odkazy odesílat prostřednictvím e-mailu nebo pomocí rychlé zprávy nebo součástí jiných dokumentů, nebude možné přeložit. Uživatelé potřebovat znát přejdete na externí adresu URL.

Pokud potřebujete podporovat některý z těchto dvou scénářů, místo odkazu překlad použijte stejné interní a externí adresy URL.  

## <a name="enable-link-translation"></a>Povolit překlad odkazu

Začínáme s překladu odkazu je stejně snadné jako kliknutí na tlačítko:

1. Přihlaste se na webu [Azure Portal](https://portal.azure.com) jako správce.
2. Přejděte na **Azure Active Directory** > **podnikové aplikace** > **všechny aplikace** > vyberte aplikaci, kterou chcete spravovat >  **Proxy aplikací**.
3. Zapnout **překládat adresy URL v těle žádosti** k **Ano**.

   ![Vyberte Ano. Pokud chcete překlad adres URL v těle žádosti](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Vyberte **Uložit** změny.

Teď když uživatelé přistoupí k této aplikaci, proxy automaticky vyhledá interní adresy URL, které byly publikované prostřednictvím Proxy aplikace ve svém tenantovi.

## <a name="send-feedback"></a>Poslat názor

Chceme, aby vaši pomoc, aby tuto funkci pro všechny vaše aplikace fungovat. Jsme vyhledat více než 30 značky v kódu HTML a CSS. Pokud máte příkladem generované odkazy, které se překlad vztahuje, pošlete fragment kódu pro [váš názor na aplikaci Proxy](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Další postup
[Použijte vlastní domény s Azure AD Application Proxy](application-proxy-configure-custom-domain.md) mít stejné interní a externí adresy URL

[Konfigurace alternativních mapování přístupu pro SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
