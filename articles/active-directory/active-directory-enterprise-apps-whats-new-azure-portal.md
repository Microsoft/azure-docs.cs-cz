---
title: Co je nového ve správě podnikových aplikací v Azure Active Directory | Dokumentace Microsoftu
description: Zjistěte, co je nového ve správě podnikových aplikací v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
editor: ''
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: fefc508679a309262d07a582fc8f5bdf9f67cfe5
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310109"
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Co je nového ve správě podnikových aplikací v Azure Active Directory 

Azure Active Directory (Azure AD) obsahuje rozšířenou podnikové aplikace nástroje pro správu, pomocí nové funkce a možnosti zjednodušit správu aplikací a efektivní.

Zde jsou některé vylepšení pro Azure AD [webu Azure portal](https://portal.azure.com).

- Galerii aplikací vylepšené prostředí, zjednodušeným aplikačním vytváření modelu a podporu pro všechny typy aplikací, které jste zvyklí. 
- Úplně rychlý start prostředí, které vám pomohou začít s pilotní nasazení vaší aplikace. 
- Nakonfigurujte zásady samoobslužných služeb jen několika kliknutími. 
- Vylepšení proxy aplikací, jednotného přihlašování a používání vlastních aplikací. ukázky, umožňuje získat víc práce než před.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Vylepšení pro aplikaci Galerie Azure Active Directory

Přidat vaše oblíbené aplikace, ať už jsou z [Galerie aplikací](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery), vlastních aplikací, které jste rozšíření do cloudu nebo vytváříte nové aplikace.  Můžete začít s tyto nové možnosti kliknutím **přidat** pod **podnikové aplikace** nebo **všechny aplikace**.
 
  ![Přidání aplikace](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

Jednou v galerii, zobrazí se vám všechny vybrané aplikace, které podporují zřizování uživatelů zobrazí front a center. Můžete procházet celou řadu různých kategorií zobrazit podrobné informace o aplikacích, které vás zajímají, nebo můžete použít vyhledávání rychle najít aplikace, které chcete integrovat.

  ![Galerie aplikací](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Přidat vlastní aplikace na jednom místě

Kromě přidání předem integrovaných aplikací z galerie, jsou vše, co prostředí konfigurace vlastní aplikace, aby byly použity k portálu pro správu klasické, teď je to možné na novém portálu. Zda jsou rozšíření aplikace pomocí proxy aplikací, integraci heslo nebo federovaného jednotného přihlašování aplikace v místním nebo vytvoření zcela nové aplikace pomocí registru aplikace, můžete získat k němu z tohoto jediného místa.

  ![Přidání vlastní aplikace](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Začít přidávat svoji vlastní aplikaci**:

1. Klikněte na tlačítko **přidat vlastní odkaz** nahoře v galerii aplikací. 
1. Uvidíte dvě možnosti před vámi: **nasadit existující aplikaci** nebo **vyvíjet nové aplikace**. Přečtěte si informace o rozdílu mezi dvěma možnostmi a jejich použití.

### <a name="deploying-existing-applications"></a>Nasazení existujících aplikací

1. Pokud máte aplikace, která už běží a chcete jenom na jeho integraci do služby Azure AD pro jednotné přihlašování nebo zřizování, zvolte **nasadit existující aplikaci** možnost. Vyberte název vaší aplikace, klikněte na tlačítko **přidat**.
1. A to je vše! Místo toho budou znát všechny podrobnosti o své aplikaci ještě před zahájením, můžete nyní nastavit fungování novou aplikaci tak, že procházení v levé nabídce a konfigurace aplikace podle vašich představ v každém okamžiku.

  ![Přidání existující aplikace s jedním kliknutím](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Vývoj nové aplikace

1. Pokud vytváříte novou aplikaci, je jednoduchý způsob získání registru aplikace doprava z galerie:
1. Klikněte na tlačítko **přidejte vlastní** z Galerie aplikací, vyberte možnost **vývoj existující aplikaci**, a zobrazí se vám rychlý odkaz přímo do prostředí přidat aplikaci.

  ![Přidání nově vyvinutých aplikací pomocí několika kliknutí](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Po přidání aplikace pomocí registru aplikace, zobrazí se zobrazí v seznamu podnikových aplikací, kde budete moct nakonfigurovat jednotné přihlašování a spravovat zásady přístupu pro novou aplikaci.

  ![Správa přístupu k nové aplikaci v podnikových aplikací](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quickstart-get-going-with-your-new-application-right-away"></a>Rychlý start: Začínáme s novou aplikaci hned 

Po přidání aplikace, je použít předem integrované nebo vlastní aplikace, jsme vytvořili přizpůsobené prostředí rychlý start vás rychle založený na nové prostředí aplikace. Jestli začnete provádět jednotlivé možnosti systematicky, budete vás provedou uživatelského rozhraní a ukazují, jak začít využívat pilotní nasazení nové aplikace co nejrychleji. 
 
  ![Nové prostředí aplikace rychlý start](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Tyto nové možnosti rychlého startu, kdykoli a pro každou aplikaci, můžete přejít kliknutím na **rychlý Start** z levé navigační nabídce aplikace.


## <a name="updated-application-proxy-configuration"></a>Konfigurace proxy serveru aktualizované aplikace

Nyní Pojďme Řekněme, jeden z nových aplikací, které jste přidali běží ve vašem místním prostředí a chcete ji integrovat s Azure AD.  Jeden z nástrojů nové věci o nové prostředí konfigurace aplikací v nové služby Azure AD portal je, že rozdělením aplikace přihlašování režimu z jeho konfigurace proxy aplikace, můžete nyní snadno vystavit heslem jednotného přihlašování nebo federovaným aplikacím které běží ve vaší podnikové síti přímo do cloudu, aniž byste museli vytvořit několik instancí aplikace.

Můžete taky nakonfigurovat některý z nové aplikace, které jste přidali pro použití se službou Azure AD Application Proxy přímo ze na novém portálu, včetně aplikací, které podporují nativní prostředí ověřování Windows.

  ![Konfigurace aplikace pro použití integrovaného ověřování Windows přihlašování – možnost](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 
Abyste mohli začít, konfigurace nativní aplikace ověřování Windows pomocí Proxy aplikace:
1. Klikněte na položku navigačního jednotné přihlašování a zvolte **integrované ověřování Windows** v části nastavení přihlašování – a nakonfigurujte nastavení podle vašich představ.
1. Nad rámec podpory těchto nových režimy ověřování, teď můžete také nahrát certifikáty z vlastních domén pro podporu aplikací běžících na zabezpečené koncové body v rámci vaší organizace.  
 
   ![Nahrává se certifikát, který se má použít s Proxy aplikací](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

1. Nahrát nový certifikát pro vaši aplikaci oblíbenými místními, klikněte na **proxy aplikací** z levé navigační nabídce, kliknutím na **certifikát** výběru a nahrání certifikátu soubor, který můžete použít k šifrování žádostí z naší koncového bodu cloudu pro vaši aplikaci.

## <a name="advanced-federated-single-sign-on-configuration"></a>Pokročilé federované jednotné přihlašování

Pro ty z vás, používáte federované aplikace už existují v konfiguraci založené na SAML přihlašování – podpora řady nových funkcí. Začněte tím teď můžete plně přizpůsobit, přidat, odebrat a mapovat existující atributy uživatele vydaný jako deklarace identity v tokenech SAML.
 
  ![Přizpůsobení atributy SAML token uživatele předaný k federované aplikaci](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)

Chcete-li zkontrolovat, že si nové federované Konfigurace jednotného přihlašování:
1. Otevřete federovaných aplikací **jednotného přihlašování** z levé navigační nabídce a ujistěte se, že "*přihlašování na základě SAML** vybraný režim. 
1. Jednou, povolte zaškrtávací políčko **atributy uživatele** záhlaví změnit všechny atributy, které jsou zahrnuté v tokenu SAML předaný k dané aplikaci.

Vám může také vytvoření, změna a správa certifikátů pro federované jednotné přihlašování, jakož i upravit, který načte oznámení, když vašeho certifikátu brzy vyprší platnost. Zobrazí se vám tyto nové možnosti v části **certifikáty** nadpis na jednotné přihlašování – podokno.
 
  ![Vytváří se nový certifikát, přizpůsobení vypršení platnosti oznámení e-mailu a certifikátu možnosti podpisu](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-parameter"></a>Parametr stav přenosu
Nakonec jsme jsme také rozšířit sadu parametrů adresy URL SAML podporujeme zahrnout **stav přenosu parametr**, což je stránka uživatele přesměrováni na uvnitř federovaných aplikací po dokončení přihlášení. To je užitečné nastavení můžete konfigurovat, pokud chcete uživatelům odeslat na konkrétní místo v rámci aplikace, aby je probíhající rychle.

  ![Nastavení parametru stav přenosu SAML](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Nastavení parametru state relay**:

1. Povolit **zobrazit pokročilé nastavení URL** zaškrtávací políčko v části **domény a adresy URL** nadpis na jednotné přihlašování v podokně Konfigurace. 
1. Zobrazí pole, které umožňují nastavit tento parametr a další nastavení SAML URL vstupní sada novou adresu URL.

## <a name="bring-your-own-password-sso-applications"></a>Přineste vlastní heslo jednotného přihlašování k aplikacím

Víme, že ne každé aplikace podporuje federace předem připravené. Možná například jeden z nových aplikací, které jste přidali má vlastní přihlašovací obrazovku, která vaši uživatelé používat uživatelské jméno a heslo pro přihlášení k aplikaci. Tyto typy aplikací stále můžete integrovat s Azure AD pomocí našich **používání vlastních aplikací** funkci, která je teď můžete nakonfigurovat na novém portálu k dispozici.
 
  ![Integrace vlastního hesla vaulting aplikací pomocí Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Abyste se funkci 'Používání vlastních aplikací'**:

1. Po nastavení jednotné přihlašování režim pro novou vlastní aplikaci, která jste přidali do **přihlašování na základě heslo**, zadejte adresu URL, kde aplikace zobrazí jeho obrazovka pro přihlášení a 
1. Klikněte na **Uložit**.  
1. Jakmile to uděláte, jsme budete automaticky scrape tuto adresu URL k zadání uživatelského jména a hesla vstupní pole a umožní vám umožňuje bezpečně přenášet hesla k dané aplikaci pomocí rozšíření prohlížeče přístupového panelu Azure AD.

## <a name="configure-self-service-application-access"></a>Konfigurace samoobslužného přístupu k aplikacím

Po přidání spoustu nových aplikací, možná budete chtít povolit uživatelům procházet a tyto aplikace přidat do své vlastní přístupových panelech, aniž byste museli jste Nepokoušejte se jako správce. Nyní v této nejnovější verzi, můžete nakonfigurovat a spravovat samoobslužného přístupu k aplikacím přímo z nového portálu.

  ![Konfigurace samoobslužného přístupu k aplikacím s heslem jednotného přihlašování aplikace](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Ke konfiguraci a správě samoobslužného přístupu k aplikacím**:

1. Abyste mohli začít, můžete vybrat **samoobslužné** levé navigační nabídce a nastavte možnost z aplikace **povolit uživatelům žádat o přístup k této aplikaci?** možnost "**Ano**". 
1. To vám umožní nakonfigurovat, kdo může schvalovat přístup k této aplikaci a které samoobslužnými uživateli skupina bude přidána. Kromě toho pokud aplikace je nakonfigurovaná pro heslo jednotného přihlašování, uvidíte také další možnost, která umožňuje volitelně povolit tyto schvalovatele, které chcete spravovat hesla přiřazené k aplikaci.

## <a name="feedback"></a>Váš názor

Věříme, že jste například vylepšenou prostředí Azure AD. Nechte prosím už zpětnou vazbu! Publikovat vaše názory a návrhy pro zlepšení **portál pro správu** část naší [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Jsme už nadšený, vytváření nových co skvělého každý den a používat vaše pokyny na obrazec a definovat, co se máme zaměřit příště.

## <a name="next-steps"></a>Další postup

Další podrobnosti najdete v tématu [Správa aplikací pomocí Azure Active Directory](manage-apps/what-is-application-management.md).



