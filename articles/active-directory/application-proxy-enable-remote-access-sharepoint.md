---
title: Povolte vzdálený přístup na SharePoint s proxy aplikace služby Azure AD | Microsoft Docs
description: Popisuje základní informace o tom, jak integrovat Azure AD Application Proxy serveru SharePoint na místě.
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
ms.date: 09/06/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 5d61ad93ad5e3ab7fdf83c1cef2fe6770089112f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Povolte vzdálený přístup na SharePoint s proxy aplikace služby Azure AD

Tento článek popisuje postup pro integraci serveru SharePoint místní Proxy aplikace služby Azure Active Directory (Azure AD).

Postup povolení vzdáleného přístupu na SharePoint s proxy aplikace služby Azure AD, postupujte podle části v tomto článku krok za krokem.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že už máte SharePoint 2013 nebo novější ve vašem prostředí. Kromě toho zvažte následující požadavky:

* SharePoint zahrnuje nativní podpora protokolu Kerberos. Uživatelé, kteří přistupují k interním lokalitám vzdáleně přes Azure AD Application Proxy tedy můžete předpokládat, tak, aby měl prostředí jednotné přihlašování (SSO).

* Tento scénář zahrnuje změny konfigurace serveru SharePoint. Doporučujeme používat pracovní prostředí. Tímto způsobem můžete nejdřív aktualizace pracovní server a pak usnadnění cyklus testování před přechodem do produkčního prostředí.

* Je nutné protokolu SSL na publikovanou adresu URL. Je potřeba mít SSL na interní web povolena a zkontrolujte, zda odkazy jsou odeslána namapované správně. Pokud jste nenakonfigurovali SSL, najdete v části [konfigurace protokolu SSL pro službu SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) pokyny. Taky se ujistěte, že počítač konektor důvěřuje certifikátu, který odešlete. (Certifikát nemusí být veřejně vydaných.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Krok 1: Nastavení jednotného přihlašování do služby SharePoint

Pro místní aplikace, které používají ověřování Windows můžete dosáhnout jednotné přihlašování (SSO) s ověřovací protokol Kerberos a funkci omezené delegování protokolu Kerberos (použitím KCD). Použitím KCD, při konfiguraci, umožňuje konektor Proxy aplikace získá token systému Windows pro uživatele, i když uživatel není přihlášený k systému Windows přímo. Další informace o použitím KCD najdete v tématu [přehled omezené delegování protokolu Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Chcete-li nastavit použitím KCD na serveru služby SharePoint, použijte postupy v následujících částech po sobě jdoucích:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Ujistěte se, že služby SharePoint běží pod účtem služby

Nejprve se ujistěte, že služby SharePoint běží pod účtem služby definované – není místní systém, místní služba nebo síťové služby. K tomu, aby hlavních názvů služby (SPN) lze připojit k platný účet. Hlavní názvy služby SPN se, jak protokol Kerberos identifikuje různé služby. A budete potřebovat účet později konfigurovat použitím KCD.

> [!NOTE]
Je potřeba mít dříve vytvořený účet služby Azure AD pro službu. Doporučujeme povolit změny automatické hesla. Další informace o úplnou sadu kroků a řešení problémů najdete v tématu [nakonfigurovat Automatická změna hesla v SharePoint 2013](https://technet.microsoft.com/library/ff724280.aspx).

Aby se zajistilo, že vaše lokality běží pod účtem služby definované, proveďte následující kroky:

1. Otevřete **Centrální správa služby SharePoint 2013** lokality.
2. Přejděte na **zabezpečení** a vyberte **konfigurovat účty služby**.
3. Vyberte **fond webových aplikací – SharePoint - 80**. Možnosti můžou mírně lišit na základě názvu vaší webové fondu, nebo pokud fondu webové ve výchozím nastavení používá protokol SSL.

  ![Možnosti pro konfiguraci účtu služby](./media/application-proxy-remote-sharepoint/service-web-application.png)

4. Pokud **vyberte účet pro tuto součást** je nastaveno na **místní služba** nebo **síťové služby**, musíte vytvořit účet. Pokud ne, jste hotovi a můžete přesunout k další části.
5. Vyberte **zaregistrovat nový spravovaný účet**. Po vytvoření účtu, je nutné nastavit **fond aplikací webové** před použitím účtu.

### <a name="configure-sharepoint-for-kerberos"></a>Konfigurace služby SharePoint pro protokol Kerberos

Můžete použitím KCD provést jednotné přihlašování k serveru SharePoint.

Konfigurace webu služby SharePoint pro ověřování protokolu Kerberos:

1. Otevřete **Centrální správa služby SharePoint 2013** lokality.
2. Přejděte na **Správa aplikací**, vyberte **správu webových aplikací**a vyberte web služby SharePoint. V tomto příkladu je **SharePoint - 80**.

  ![Vyberte web služby SharePoint](./media/application-proxy-remote-sharepoint/manage-web-applications.png)

3. Klikněte na tlačítko **zprostředkovatele ověřování** na panelu nástrojů.
4. V **zprostředkovatele ověřování** pole, klikněte na tlačítko **výchozí pásmo** zobrazení nastavení.
5. V **upravit ověřování** dialogové okno pole, posuňte se dolů, dokud neuvidíte **typy ověřování deklarací identity**. Ujistěte se, že oba **povolit ověřování systému Windows** a **integrované ověřování systému Windows** jsou vybrány.
6. V rozevíracím seznamu pro pole integrované ověřování systému Windows, ujistěte se, že **Vyjednat (Kerberos)** je vybrána.

  ![Dialog Upravit ověřování](./media/application-proxy-remote-sharepoint/service-edit-authentication.png)

7. V dolní části **upravit ověřování** dialogové okno, klikněte na tlačítko **Uložit**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Nastavte hlavní název služby pro účet služby SharePoint

Než začnete konfigurovat použitím KCD, musíte k identifikaci služby SharePoint spuštěna jako účet služby, který jste nakonfigurovali. Nastavením názvu SPN identifikaci služby. Další informace najdete v tématu [hlavní názvy služby](https://technet.microsoft.com/library/cc961723.aspx).

Není ve formátu hlavního názvu služby:

```
<service class>/<host>:<port>
```

Ve formátu hlavního názvu služby:

* _Třída služby_ je jedinečný název pro službu. Pro službu SharePoint, můžete použít **HTTP**.

* _hostitele_ je plně kvalifikované domény nebo název pro rozhraní NetBIOS hostitele, který je služba spuštěná na. Pro web služby SharePoint tento text může být nutné adresu URL webu, v závislosti na verzi služby IIS, který používáte.

* _port_ je volitelný.

Pokud je plně kvalifikovaný název domény serveru SharePoint:

```
sharepoint.demo.o365identity.us
```

Pak je hlavní název služby:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Může také musíte nastavit SPN pro určité weby na vašem serveru. Další informace najdete v tématu [ověřování protokolem Kerberos konfigurace](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Zaměřte do části "Vytvoření hlavní názvy služby pro vaší webové aplikace s použitím ověřování protokolu Kerberos".

Nejjednodušším způsobem, jak vám umožní nastavit SPN je postupujte podle formáty hlavního názvu služby, které již mohou být k dispozici pro vaše lokality. Zkopírujte tyto hlavní názvy služby SPN zaregistrovat u účtu služby. Použijte následující postup:

1. Přejděte na web s hlavní název služby z jiného počítače.
 Když to uděláte, relevantní sadu lístky protokolu Kerberos se uloží do mezipaměti na počítači. Tyto lístky obsahují SPN je cílový webový server, který jste vyhledali.

2. Může vyžádat hlavní název služby pro danou lokalitu pomocí nástroje názvem [Klist](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). V příkazovém okně, které běží ve stejné oblasti jako uživatel, který získat přístup k webu v prohlížeči spusťte následující příkaz:
```
Klist
```
Klist pak vrátí sadu cíl SPN. V tomto příkladu je zvýrazněná hodnota hlavní název služby, který je potřeba:

  ![Příklad Klist výsledky](./media/application-proxy-remote-sharepoint/remote-sharepoint-target-service.png)

4. Teď, když máte hlavní název služby, ujistěte se, zda je správně nakonfigurovaná na účet služby, které jste nastavili pro webovou aplikaci dříve. Spusťte následující příkaz z příkazového řádku jako správce domény:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Tento příkaz nastaví název SPN pro účet služby SharePoint spuštěna jako _demo\sp_svc_.

 Nahraďte _http/sharepoint.demo.o365identity.us_ s hlavní název serveru a _demo\sp_svc_ pomocí účtu služby ve vašem prostředí. Příkaz Setspn hledá SPN před přidáním ho. V takovém případě může dojít **duplicitní hodnota SPN** chyby. Pokud se zobrazí tato chyba, ujistěte se, že hodnota je přidružen k účtu služby.

Můžete ověřit, že byl přidán SPN spuštěním příkazu Setspn -l možnost. Další informace o tomto příkazu najdete v tématu [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Zkontrolujte, že konektor je nastavená jako důvěryhodné delegáta do služby SharePoint

Nakonfigurujte použitím KCD tak, aby služba Azure AD Application Proxy můžete delegovat identit uživatelů ve službě SharePoint. Nakonfigurujte použitím KCD povolením konektoru Proxy aplikace pro načtení lístky protokolu Kerberos pro uživatele, kteří byli ověřeni ve službě Azure AD. Potom tento server v takovém případě předá kontextu cílová aplikace nebo služby SharePoint.

Pokud chcete nakonfigurovat použitím KCD, opakujte pro každý počítač konektor následující kroky:

1. Přihlaste se jako správce domény na řadič domény a pak otevřete **Active Directory Users and Computers**.
2. Hledání počítače, který konektor běží na. V tomto příkladu je stejný server služby SharePoint.
3. Klikněte dvakrát na počítač a klikněte **delegování** kartě.
4. Ujistěte se, jestli jsou nastavení delegování nastavené **důvěřovat tomuto počítači pro delegování pouze určeným službám**. Pak vyberte **používající libovolný protokol pro ověřování**.

  ![Nastavení delegování](./media/application-proxy-remote-sharepoint/delegation-box.png)

5. Klikněte **přidat** tlačítko, klikněte na tlačítko **uživatele nebo počítače**a vyhledejte účet služby.

  ![Přidání názvu SPN pro účet služby](./media/application-proxy-remote-sharepoint/users-computers.png)

6. V seznamu SPN vyberte ten, který jste dříve vytvořili pro účet služby.
7. Klikněte na **OK**. Klikněte na tlačítko **OK** znovu a uložte změny.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Krok 2: Povolení vzdáleného přístupu do služby SharePoint

Teď, když jste povolili SharePoint pro protokolu Kerberos a použitím nakonfigurované KCD, jste připraveni publikovat farmy služby SharePoint pro vzdálený přístup prostřednictvím proxy aplikace služby Azure AD.

1. Publikování webu služby SharePoint s následujícím nastavením. Podrobné pokyny najdete v tématu [publikování aplikací pomocí proxy aplikace služby Azure AD](application-proxy-publish-azure-portal.md). 
   - **Interní adresa URL**: adresa URL webu služby SharePoint interně jako **https://SharePoint/**. V tomto příkladu, nezapomeňte použít **https**
   - **Metoda předběžného ověření**: Azure Active Directory
   - **Převede adresu URL v hlavičkách**: Ne

   >[!TIP]
   >Používá služby SharePoint _Hlavička hostitele_ hodnotu k vyhledání webu. Generuje také odkazy na základě této hodnoty. Čistý efekt je, že odkazy, které generuje SharePoint není publikovanou adresu URL, která je správně nastavený na použití externí adresu URL. Nastavení na hodnotu **Ano** taky umožňuje konektor předávat požadavek na back-end aplikace. Nicméně, nastavte tuto hodnotu na **ne** znamená, že konektor nebude odesílají název interní hostitele. Místo toho konektor odešle hlavičku hostitele jako adresu URL publikované na back-end aplikace.

   ![Publikovat jako aplikaci služby SharePoint](./media/application-proxy-remote-sharepoint/publish-app.png)

2. Po publikování aplikace nakonfigurujte nastavení jednotného přihlašování pomocí následujících kroků:

   1. Na stránce aplikace na portálu vyberte **jednotného přihlašování**.
   2. Pro režim jednoho přihlášení, vyberte **integrované ověřování systému Windows**.
   3. Interní název SPN aplikace nastavte na hodnotu, která jste nastavili dříve. V tomto příkladu, který by **http/sharepoint.demo.o365identity.us**.

   ![Konfigurace integrovaného ověřování systému Windows pro jednotné přihlašování](./media/application-proxy-remote-sharepoint/configure-iwa.png)

3. Chcete-li dokončit nastavení aplikace, přejděte na **uživatelů a skupin** části a přiřazení uživatelů s přístupem k této aplikaci. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Krok 3: Zajistěte, aby SharePoint ví o externí adresu URL

V posledním kroku je zajistit, že služby SharePoint můžete najít serveru v závislosti na externí adresu URL, tak, aby vykreslením odkazy podle této externí adresu URL. To uděláte tak, že nakonfigurujete mapování alternativních adres URL pro web služby SharePoint.

1. Otevřete **Centrální správa služby SharePoint 2013** lokality.
2. V části **nastavení systému**, vyberte **konfigurace mapování alternativních adres**. Otevře se pole mapování alternativních adres URL.

  ![Alternativní pole mapování adres URL](./media/application-proxy-remote-sharepoint/alternate-access1.png)

3. V rozevíracím seznamu vedle položky **kolekce mapování alternativních adres**, vyberte **změnit alternativní přístup mapování kolekci**.
4. Vyberte svou lokalitu – například **SharePoint - 80**.
5. Můžete přidat adresu URL publikované jako interní adresa URL nebo veřejnou adresu URL. Tento příklad používá veřejnou adresu URL jako extranetu.
6. Klikněte na tlačítko **Upravit veřejné adresy URL** v **Extranet** cestu a pak zadejte externí adresu URL, která byla vytvořena, když jste publikovali aplikaci. Zadejte například **https://sharepoint-iddemo.msappproxy.net**.

  ![Vstupující do cesty](./media/application-proxy-remote-sharepoint/alternate-access3.png)

7. Klikněte na **Uložit**.

Nyní můžete k webu služby SharePoint externě prostřednictvím proxy aplikace služby Azure AD.

## <a name="next-steps"></a>Další postup

- [Práce s vlastní domény v Azure AD Application Proxy](active-directory-application-proxy-custom-domains.md)
- [Pochopení konektory proxy aplikace služby Azure AD](application-proxy-understand-connectors.md)

