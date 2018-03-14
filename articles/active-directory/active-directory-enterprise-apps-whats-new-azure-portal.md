---
title: "Co je nového v nástroji Správa podniková aplikace v Azure Active Directory | Microsoft Docs"
description: "Zjistěte, co je nového v nástroji Správa podniková aplikace v Azure Active Directory."
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 949eae51c469205489153c271a7c20fcc843ad46
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Co je nového v nástroji Správa podniková aplikace v Azure Active Directory 

Azure Active Directory (Azure AD) je vylepšené enterprise nástroje pro správu aplikací, s nové funkce a možnosti, aby Správa aplikací jednodušší a efektivní.

Zde jsou některé rozšířením pro službu Azure AD v [portál Azure](https://portal.azure.com).

- Vylepšené aplikace galerii prostředí s vytvoření modelu zjednodušené aplikací a podpora pro všechny typy aplikací, které jste použili. 
- Zcela nový rychlý start prostředí, které vám může pomoct zprovoznění pilotní nasazení vaší aplikace. 
- Nakonfigurujte zásady samoobslužné služby pomocí několika kliknutí. 
- Vylepšení proxy aplikace jednotného přihlašování konfigurace a přineste si vlastní prostředí aplikace umožňuje získat více done než před.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Vylepšení galerii aplikací Azure Active Directory

Vaše oblíbené aplikace přidat, ať už z [galerii aplikací](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), vlastních aplikací, které jste rozšíření do cloudu nebo nové aplikace, které vyvíjíte.  Můžete začít používat s toto nové prostředí kliknutím **přidat** na **podnikové aplikace, které** přehled nebo **všechny aplikace** okna.
 
  ![Přidání aplikace](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

Jednou v galerii, uvidíte, že všechny naše vybrané aplikace, které podporují zřizování uživatelů zobrazí přední a center.  Můžete procházet nejrůznějším různých kategorií přejdete do aplikace, které se zajímáte o nebo možnosti vyhledávání můžete rychle najít aplikací, které chcete integrovat.

  ![Galerii aplikací](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Přidat vlastní aplikace z jednoho místa

Kromě přidání předem integrovaných aplikací z galerie, všechny, které narazí konfiguraci vlastní aplikace měla použitá při na portálu classic správu jsou nyní v nového portálu. Zda jsou rozšíření aplikace v místním prostředí pomocí proxy aplikace, integraci vlastní heslo nebo federovaného jednotného přihlašování k aplikaci, nebo vytvoření zcela nové aplikace pomocí klíče registru aplikace, můžete získat k němu z jediné jeden.

  ![Přidání vlastní aplikace](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Abyste mohli začít přidání vlastní aplikace**:

1. Klikněte **přidat odkaz na vaši vlastní** v horní části galerii aplikací. 
2. Uvidíte dvě možnosti před jste: **nasadit existující aplikaci** nebo **vytvořte novou aplikaci**. Přečtěte si informace o rozdílu mezi dvě možnosti a jejich použití.

### <a name="deploying-existing-applications"></a>Existující nasazení aplikace

1. Pokud máte k dispozici již spuštěné aplikace a chcete jen na jeho integraci do Azure AD pro jednotné přihlašování nebo zřizování, zvolte **nasadit existující aplikaci** možnost. Vyberte název vaší aplikace, klikněte na tlačítko **přidat**.
2. A to je vše! Místo nepotřebuje vědět všechny podrobnosti o aplikaci předem, můžete nyní nastavit jak novou aplikaci funguje tak, že procházení v levé nabídce a konfigurace aplikace libovolně kdykoli.

  ![Přidání stávající aplikace s jedním kliknutím](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Vývoj nových aplikací

1. Pokud vytváříte novou aplikaci, je snadný způsob získání registru aplikace pravé z galerie:
2. Klikněte na **přidat vlastní** možnost z Galerie aplikace, vyberte **vyvíjet existující aplikaci** volba a zobrazí se odkaz rychlé právo na používání aplikací přidat.

  ![Přidání aplikace nově vyvinutých stačí jen pár kliknutí](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>Jakmile přidáte aplikace pomocí klíče registru aplikace, zobrazí se zobrazí v seznamu podnikové aplikace, kde budete moci konfigurovat jednotné přihlašování a spravovat zásady přístupu pro novou aplikaci.

  ![Správa přístupu k nové aplikaci v podnikových aplikací](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quick-start-get-going-with-your-new-application-right-away"></a>Rychlý start: Začínáme s novou aplikaci hned 

Po přidání aplikace, jestli se předem integrované nebo vlastní aplikace, vytvořili jsme šité na míru prostředí úvodní, abyste si rychle založena na nové prostředí aplikace. Pokud budete postupovat systematičtěji podle jednotlivých možností, budete jsme vás provede procesem uživatelského rozhraní a ukazují, jak co nejrychleji začít s pilotní nasazení nové aplikace. 
 
  ![Nové aplikace rychlý start prostředí](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Toto nové prostředí úvodní kdykoli a pro každou aplikaci, můžete taky navštívit kliknutím na **úvodní** z levé navigační nabídce aplikace.


## <a name="updated-application-proxy-configuration"></a>Konfigurace proxy aktualizované aplikace
Nyní Pojďme indikované jeden z nové aplikace, které jste přidali běží v místním prostředí a chcete integrovat se službou Azure AD.  Jedním z nástrojů nových informací o nové rozhraní konfigurace aplikací v nové službě Azure AD portál je, že rozdělením aplikace přihlašování v režimu z jeho konfigurace proxy aplikace nyní můžete snadno vystavit heslo jednotného přihlašování nebo federovaných aplikací, které běží ve vaší podnikové síti přímo do cloudu, aniž by bylo nutné vytvořit více instancí aplikace.

Kromě toho teď můžete také konfigurovat žádné nové aplikace, které jste přidali pro použití se službou Azure AD Application Proxy přímo z portálu nový, včetně aplikací, které podporují nativní prostředí ověřování Windows.

  ![Konfigurace aplikace použít možnost přihlášení integrované ověřování systému Windows](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

Chcete začít konfigurovat nativní aplikaci ověřování systému Windows s Proxy aplikace:
1. Kliknutím na položku jednoho navigačního přihlašování a zvolte **integrované ověřování systému Windows** z okna Nastavení přihlášení a nakonfigurujte nastavení libovolně.
2. Nad podporuje tyto nové režimy ověřování, teď můžete také nahrát certifikáty z vlastní domény pro podporu aplikací běžících na zabezpečené koncové body v rámci vaší organizace.  
 
   ![Nahrávání certifikátu pro použití s Proxy aplikace](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. Pokud chcete nahrát nový certifikát pro vaše oblíbené místní aplikace, klikněte na **proxy aplikace** možnost z levé navigační nabídky, klikněte na tlačítko **certifikát** selektor a nahrání souboru certifikátu můžeme použít k šifrování požadavky z našich koncového bodu cloudu do vaší aplikace.

## <a name="advanced-federated-single-sign-on-configuration"></a>Pokročilé federované přihlašování jednotné

U těch, které používáte federovaným aplikacím dnes existuje mnoho nových funkcí v okně přihlašování v konfiguraci na základě SAML. Abyste mohli začít teď můžete plně přizpůsobit, přidat, odebrat a mapovat existující uživatelské atributy vystaví jako deklarace identity v tokenech SAML.
 
  ![Přizpůsobení atributy SAML token uživatele předaný federovaných aplikací](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


Zkontrolujte, že se nový federovaný Konfigurace jednotného přihlašování:
1. Otevřete federovaných aplikací **jednotného přihlašování** okno z levé navigační nabídku a ujistěte se, že '*na základě SAML přihlašování** vybrat režim. 
2. Existuje, povolit políčka pod jednou **uživatelské atributy** záhlaví upravit všechny atributy, které jsou součástí tokenu SAML předaný tuto aplikaci.

Můžete můžete také vytvořit, výměny a spravovat certifikáty používané pro federované jednotné přihlašování, jakož i upravit, který získá upozorněni, když váš certifikát již brzy vyprší. Zobrazí se tyto nové možnosti v části **certifikáty** záhlaví na stejné jeden přihlašování v okně.
 
  ![Vytvoření nového certifikátu, přizpůsobení vypršení platnosti oznámení e-mailu a možnosti podepsání certifikátu](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-paramenter"></a>Stav předávání paramenter
Nakonec jsme jste také rozšířit sadu parametrů SAML URL podporujeme zahrnout **parametru State předávání**, což je stránce uživatelům budou zobrazovat uvnitř federovaných aplikací po dokončení přihlášení. Toto je velmi užitečná nastavení konfigurace, pokud chcete uživatelům odeslat na konkrétní místo v aplikaci je nelze vrátit probíhající rychle.

  ![Nastavení parametru SAML předávání stavu](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Nastavení parametru stavu předávání**:

1. Povolit **zobrazit upřesňující nastavení adresy URL** políčko v části **domény a adresy URL** záhlaví na jednotné přihlašování v okně konfigurace. 
2. Jakmile to uděláte, zobrazí se, že sadu nové adrese URL vstupních polí se zobrazují, které vám umožní nastavit tyto a další nastavení adresy URL SAML.

## <a name="bring-your-own-password-sso-applications"></a>Přineste vlastní heslo aplikace jednotného přihlašování

Víme, že ne každé aplikace podporuje federační okamžitě po nasazení. Může být například jeden nové aplikace, které jste přidali má vlastní přihlašovací obrazovku, kterou budou uživatelé používat uživatelské jméno a heslo pro přihlášení k aplikaci. Tyto typy aplikací můžete stále integraci s Azure AD pomocí našich **přineste si vlastní aplikace** funkci, která je nyní k dispozici, můžete nakonfigurovat v nového portálu.
 
  ![Integrace vlastního hesla vaulting aplikací s Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Rezervovat funkci "přineste vlastní aplikace,**:

1. Po nastavení jedné přihlašování v režimu pro novou vlastní aplikaci, kterou jste přidali do **založené na heslech přihlašování**, zadejte adresu URL, kde aplikace vykreslí jeho obrazovka pro přihlášení a klikněte na tlačítko **Uložit**.  
2. Jakmile to uděláte, jsme budete automaticky scrape tuto adresu URL pro uživatelské jméno a heslo vstupní pole a vám umožní používat Azure AD bezpečně přenést hesla k dané aplikaci pomocí rozšíření přístup k panelu prohlížeče.

## <a name="configure-self-service-application-access"></a>Konfigurace přístupu k aplikaci Samoobslužné služby

Po přidání spoustu nové aplikace, možná budete chtít povolit uživatelům procházet a přidejte k vlastní panelů přístup k těmto aplikacím, aniž by museli jste Nepokoušejte se jako správce. Nyní tato nejnovější verze, můžete nakonfigurovat a spravovat přístup k aplikaci Samoobslužné služby přímo z nového portálu.

  ![Konfigurace samoobslužné služby aplikace přístup k zadání hesla aplikace jednotného přihlašování](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Konfigurovat a spravovat přístup k aplikaci Samoobslužné služby**:

1. Chcete-li začít, můžete vybrat **samoobslužné služby** levé navigační nabídku a nastavit možnost z aplikace **povolit uživatelům žádat o přístup k této aplikaci?** možnost k '**Ano**'. 
2. To vám umožní nakonfigurovat, kdo může schválit přístup k této aplikaci a kteří uživatelé samoobslužné služby skupiny budou přidáni. Kromě toho pokud aplikace je nakonfigurovaná pro heslo jednotného přihlašování, se také zobrazí jinou možnost, která umožňuje volitelně povolit tyto schvalovatelů spravovat hesla přiřazené k aplikaci.

##<a name="feedback"></a>Váš názor

Věříme, že je jako vylepšenou prostředí Azure AD. Prosím udržovat zpětnou vazbu, než dorazí! POST vaše názory a návrhy pro zlepšení **portál pro správu** části našich [fóru pro zpětnou vazbu](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Jsme se vzrušení o vytváření nástrojů nové vlastní položky každý den a použijte vaše pokyny na obrazec a definovat, co se máme zaměřit příště.

## <a name="next-steps"></a>Další postup

Další podrobnosti najdete v tématu [Správa aplikací pomocí služby Azure Active Directory](active-directory-enable-sso-scenario.md).



