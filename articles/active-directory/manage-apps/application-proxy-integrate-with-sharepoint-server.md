---
title: Povolit vzdálený přístup k Sharepointu pomocí Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Popisuje základní informace o tom, jak integrovat Azure AD Application Proxy místnímu serveru SharePoint.
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
ms.date: 10/19/2018
ms.author: barbkess
ms.reviewer: japere
ms.custom: it-pro
ms.openlocfilehash: ad0ab8790c9d66dbba87d466e9661ef8cf33eef3
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49456894"
---
# <a name="enable-remote-access-to-sharepoint-with-azure-ad-application-proxy"></a>Povolit vzdálený přístup k Sharepointu pomocí Azure AD Application Proxy

Tento článek popisuje, jak integrovat Proxy aplikací Azure Active Directory (Azure AD) místnímu serveru SharePoint.

Pokud chcete povolit vzdálený přístup k Sharepointu pomocí Proxy aplikací Azure AD, postupujte podle části v tomto článku krok za krokem.

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste již SharePoint 2013 nebo novější ve vašem prostředí. Kromě toho zvažte následující požadavky:

* SharePoint obsahuje nativní podpora protokolu Kerberos. Uživatelé, kteří přistupují k interním lokalitám vzdáleně prostřednictvím Proxy aplikací Azure AD, proto můžete předpokládat mít možnosti jednotného přihlašování (SSO).

* Tento scénář obsahuje změny konfigurace serveru SharePoint. Doporučujeme používat pracovní prostředí. Tímto způsobem můžete nejdřív proveďte aktualizace na testovacím serveru a potom usnadnění testovacího cyklu před přechodem do produkčního prostředí.

* Požadujeme, aby protokolu SSL na publikovanou adresu URL. Budete se muset ujistit, že odkazy jsou odeslána/namapované správně povoleno na interní web zabezpečení SSL. Pokud jste neprovedli konfiguraci protokolu SSL, přečtěte si téma [konfigurace SSL pro službu SharePoint 2013](https://blogs.msdn.microsoft.com/fabdulwahab/2013/01/20/configure-ssl-for-sharepoint-2013) pokyny. Také se ujistěte, že počítač konektor důvěřuje certifikátu, který zadáte. (Certifikát nemusí být veřejně vydaných.)

## <a name="step-1-set-up-single-sign-on-to-sharepoint"></a>Krok 1: Nastavení jednotného přihlašování k Sharepointu

Pro místní aplikace, které používají ověřování Windows můžete dosáhnout jednotného přihlašování (SSO) s ověřovací protokol Kerberos a funkci s názvem omezené delegování protokolu Kerberos (KCD). KCD, při konfiguraci, umožňuje konektor Proxy aplikací k získání tokenu Windows pro uživatele, i v případě, že uživatel není přihlášený k Windows přímo. Další informace o KCD najdete v tématu [přehled omezeného delegování protokolu Kerberos](https://technet.microsoft.com/library/jj553400.aspx).

Chcete-li nastavovat KCD pro SharePoint server, použijte postupy v následujících částech sekvenční:

### <a name="ensure-that-sharepoint-is-running-under-a-service-account"></a>Ujistěte se, že SharePoint je spuštěný pod účtem služby

Nejprve se ujistěte, že SharePoint je spuštěný pod účtem definované služby – není místní systém, místní služba nebo účet network service. To proveďte tak, aby hlavní názvy služby (SPN) můžete připojit k platný účet. Hlavní názvy služby jsou způsobu, jakým protokol Kerberos identifikuje různé služby. A budete potřebovat účet později ke konfiguraci KCD.

> [!NOTE]
Musíte mít dříve vytvořený účet služby Azure AD pro službu. Doporučujeme vám umožňují automatickou změnu hesel. Další informace o úplnou sadu kroků a řešení problémů najdete v tématu [konfigurovat automatickou změnu hesel v Sharepointu 2013](https://technet.microsoft.com/library/ff724280.aspx).

Aby bylo zajištěno, že vaše weby běží pod účtem služby definované, postupujte následovně:

1. Otevřít **centrální správy služby SharePoint 2013** lokality.
2. Přejděte na **zabezpečení** a vyberte **konfigurovat účty služby**.
3. Vyberte **fond webových aplikací – SharePoint - 80**. Možnosti můžou mírně lišit podle názvu fondu web, nebo pokud fondu web ve výchozím nastavení používá protokol SSL.

  ![Možnosti pro konfiguraci účtu služby](./media/application-proxy-integrate-with-sharepoint-server/service-web-application.png)

4. Pokud **vyberte účet pro tuto součást** je nastaveno na **místní služba** nebo **síťová služba**, budete muset vytvořit účet. Pokud ne, dokončení a můžete přejít k další části.
5. Vyberte **zaregistrovat nový účet spravovaný**. Po vytvoření účtu je nutné nastavit **fond webových aplikací** než použijete tento účet.

### <a name="configure-sharepoint-for-kerberos"></a>Konfigurace Sharepointu pro protokol Kerberos

Použijete KCD provádět jednotné přihlašování k serveru SharePoint.

Konfigurace webu služby SharePoint pro ověřování pomocí protokolu Kerberos:

1. Otevřít **centrální správy služby SharePoint 2013** lokality.
2. Přejděte na **správy aplikací**vyberte **spravovat webové aplikace**a vyberte web služby SharePoint. V tomto příkladu je **SharePoint - 80**.

  ![Vyberte web služby SharePoint](./media/application-proxy-integrate-with-sharepoint-server/manage-web-applications.png)

3. Klikněte na tlačítko **zprostředkovatelé ověřování** na panelu nástrojů.
4. V **zprostředkovatelé ověřování** klikněte **výchozí zóna** zobrazíte nastavení.
5. V **upravit ověřování** dialogovém okně přejděte dolů, dokud se nezobrazí **typy ověřování deklarací identity**. Ujistěte se, že oba **povolit ověřování Windows** a **integrované ověřování Windows** jsou vybrány.
6. V rozevíracím seznamu pro pole integrované ověřování Windows, ujistěte se, že **Vyjednat (Kerberos)** zaškrtnuto.

  ![Ověřování dialogové okno Upravit](./media/application-proxy-integrate-with-sharepoint-server/service-edit-authentication.png)

7. V dolní části **upravit ověřování** dialogové okno, klikněte na tlačítko **Uložit**.

### <a name="set-a-service-principal-name-for-the-sharepoint-service-account"></a>Nastavte hlavní název služby pro účet služby SharePoint

Než začnete konfigurovat KCD, je potřeba identifikovat služby SharePoint spuštěna jako účet služby, který jste nakonfigurovali. Nastavením název SPN identifikaci služby. Další informace najdete v tématu [hlavní názvy služby](https://technet.microsoft.com/library/cc961723.aspx).

Formát hlavního názvu služby je:

```
<service class>/<host>:<port>
```

Ve formátu hlavního názvu služby:

* _Služba třídy_ je jedinečný název pro službu. Pro službu SharePoint, můžete použít **HTTP**.

* _Hostitel_ je plně kvalifikované domény nebo název NetBIOS, který služba běží na hostiteli. Pro web služby SharePoint může být nutné tento text bude adresa URL webu, v závislosti na verzi služby IIS, který používáte.

* _port_ je volitelný.

Pokud je plně kvalifikovaný název domény serveru SharePoint:

```
sharepoint.demo.o365identity.us
```

Pak je hlavní název služby:

```
HTTP/sharepoint.demo.o365identity.us demo
```

Může být také potřeba nastavit hlavní názvy služby pro určité weby na vašem serveru. Další informace najdete v tématu [ověřování protokolem Kerberos konfigurace](https://technet.microsoft.com/library/cc263449(v=office.12).aspx). Pozornosti do části "Vytvoření hlavní názvy služby pro vaše webové aplikace pomocí ověřování protokolu Kerberos".

Nejjednodušší způsob, jak si máte nastavit hlavní názvy služby má následovat formáty hlavního názvu služby, které již nemusí být k dispozici pro weby. Zkopírujte tyto hlavní názvy služby SPN zaregistrovat pro účet služby. Použijte následující postup:

1. Přejděte na web pomocí hlavního názvu služby z jiného počítače.
 Když použijete, relevantní sada lístky protokolu Kerberos je uložit do mezipaměti na počítači. Tyto lístky obsahovat hlavní název služby, který jste vyhledali cílového webu.

2. Hlavní název služby pro danou lokalitu si můžete vyžádat pomocí nástroje volá [příkaz](http://web.mit.edu/kerberos/krb5-devel/doc/user/user_commands/klist.html). V příkazovém okně, na kterém běží ve stejném kontextu jako uživatel, který se připojil k serveru v prohlížeči spusťte následující příkaz:
```
Klist
```
Příkaz vrátí sadu cílový hlavní názvy služby. V tomto příkladu je zvýrazněné hodnoty, který je nezbytný hlavní název služby:

  ![Příklad příkaz výsledků](./media/application-proxy-integrate-with-sharepoint-server/remote-sharepoint-target-service.png)

4. Teď, když máte hlavní název služby, ujistěte se, že je správně nakonfigurovaná na účet služby, které jste nastavili dříve webové aplikace. Spusťte následující příkaz z příkazového řádku jako správce domény:

 ```
 setspn -S http/sharepoint.demo.o365identity.us demo\sp_svc
 ```

 Tento příkaz nastaví název SPN pro účet služby SharePoint spuštěna jako _demo\sp_svc_.

 Nahraďte _http/sharepoint.demo.o365identity.us_ pomocí hlavního názvu služby pro váš server a _demo\sp_svc_ pomocí účtu služby ve vašem prostředí. Příkaz Setspn hledá hlavního názvu služby předtím, než ho přidá. V takovém případě se zobrazí **duplicitní hodnota hlavního názvu služby** chyby. Pokud se zobrazí tato chyba, ujistěte se, že hodnota je přidružená k účtu služby.

Můžete ověřit, že hlavní název služby byl přidán spuštěním příkazu Setspn -l možnost. Další informace o tomto příkazu najdete v tématu [Setspn](https://technet.microsoft.com/library/cc731241.aspx).

### <a name="ensure-that-the-connector-is-set-as-a-trusted-delegate-to-sharepoint"></a>Ujistěte se, že konektor je nastaven jako důvěryhodné delegáta k Sharepointu

Nakonfigurujte KCD tak, aby služba Azure AD Application Proxy můžete delegovat identit uživatelů ve službě SharePoint. Nakonfigurujte KCD povolením konektoru Proxy aplikace načíst lístky protokolu Kerberos pro uživatele, kteří byli ověřeni ve službě Azure AD. Potom tento server v tomto případě předá kontextu do cílové aplikace nebo služby SharePoint.

Pokud chcete nakonfigurovat KCD, opakujte následující kroky pro každý počítač konektoru:

1. Přihlaste se jako správce domény na řadič domény a pak otevřete **Active Directory Users and Computers**.
2. Najděte, na kterém běží konektor na počítač. V tomto příkladu je stejný server SharePoint.
3. Klikněte dvakrát na počítač a potom klikněte na tlačítko **delegování** kartu.
4. Ujistěte se, že jsou nastavení delegování **důvěřovat tomuto počítači pro delegování pouze určeným službám**. Vyberte **použití libovolného protokolu pro ověřování**.

  ![Nastavení delegace](./media/application-proxy-integrate-with-sharepoint-server/delegation-box.png)

5. Klikněte na tlačítko **přidat** tlačítko, klikněte na tlačítko **uživatele nebo počítače**a vyhledejte účet služby.

  ![Přidání hlavního názvu služby pro účet služby](./media/application-proxy-integrate-with-sharepoint-server/users-computers.png)

6. V seznamu hlavních názvů služby vyberte ten, který jste dříve vytvořili pro účet služby.
7. Klikněte na **OK**. Klikněte na tlačítko **OK** znovu a uložte změny.

## <a name="step-2-enable-remote-access-to-sharepoint"></a>Krok 2: Povolení vzdáleného přístupu k Sharepointu

Teď, když jste povolili Sharepointu pro protokol Kerberos a nakonfigurované KCD, jste připraveni publikovat farmy služby SharePoint pro vzdálený přístup prostřednictvím Proxy aplikací Azure AD.

1. Publikování webu služby SharePoint s následujícím nastavením. Podrobné pokyny najdete v tématu [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-publish-azure-portal.md). 
   - **Interní adresa URL**: adresa URL Sharepointového webu interně, jako například **https://SharePoint/**. V tomto příkladu je nutné použít **https**
   - **Metoda předběžného ověření**: Azure Active Directory
   - **Přeložit adresy URL v hlavičkách**: Ne

   >[!TIP]
   >SharePoint používá _hlavičku hostitele_ hodnotu pro vyhledávání webu. Generuje také odkazy na základě této hodnoty. Výsledkem je, že je odkaz, který SharePoint vygeneruje publikované adresy URL, která je správně nastavený na použití externí adresu URL. Nastavením této hodnoty na **Ano** také povolí konektor k předání požadavku do back endové aplikace. Nicméně, nastavením této hodnoty na **ne** znamená, že konektor nebude odesílat název interního hostitele. Místo toho konektor odešle hlavičku hostitele jako adresu URL publikované na back endové aplikace.

   ![Publikovat jako aplikaci služby SharePoint](./media/application-proxy-integrate-with-sharepoint-server/publish-app.png)

2. Po publikování aplikace konfigurujte nastavení jednotného přihlašování pomocí následujících kroků:

   1. Na stránce aplikace na portálu vyberte **jednotného přihlašování**.
   2. Režim jednotného přihlašování, vyberte **integrované ověřování Windows**.
   3. Vnitřní hlavní název služby aplikace nastavte na hodnotu, která jste nastavili dříve. V tomto příkladu, který by **http/sharepoint.demo.o365identity.us**.

   ![Konfigurace ověření integrované Windows pro jednotné přihlašování](./media/application-proxy-integrate-with-sharepoint-server/configure-iwa.png)

3. Abychom mohli dokončit nastavování vaší aplikace, přejděte na **uživatelů a skupin** části a přiřadit uživatelům přístup k této aplikaci. 

## <a name="step-3-ensure-that-sharepoint-knows-about-the-external-url"></a>Krok 3: Ujistěte se, že SharePoint ví o externí adresu URL

V posledním kroku je zajistit, že služby SharePoint můžete najít web založený na externí adresu URL tak, aby se vykresluje odkazy na základě této externí adresy URL. To provedete tím, že nakonfigurujete mapování alternativních adres URL pro Sharepointový Web.

1. Otevřít **centrální správy služby SharePoint 2013** lokality.
2. V části **nastavení systému**vyberte **konfigurace mapování alternativních adres URL**. Otevře se pole mapování alternativních adres URL.

  ![Alternativní mapování pole](./media/application-proxy-integrate-with-sharepoint-server/alternate-access1.png)

3. V rozevíracím seznamu vedle **kolekce mapování alternativních adres**vyberte **změnit alternativní přístup mapování kolekci**.
4. Vyberte svou lokalitu – například **SharePoint - 80**.
5. Můžete přidat adresu URL publikované jako interní adresa URL nebo veřejnou adresu URL. Tento příklad používá veřejnou adresu URL jako extranetu. Pokud používáte vlastní port nezapomeňte zadat vlastní port v adrese URL.
6. Klikněte na tlačítko **Upravit veřejné adresy URL** v **Extranet** cestu a pak zadejte externí adresu URL, který byl vytvořen při publikování aplikace. Zadejte například **https://sharepoint-iddemo.msappproxy.net**.

  ![Vstupující do cesty](./media/application-proxy-integrate-with-sharepoint-server/alternate-access3.png)

7. Klikněte na **Uložit**.

Můžete teď přístup k webu služby SharePoint externě prostřednictvím Proxy aplikací Azure AD.

## <a name="next-steps"></a>Další postup

- [Práce s vlastními doménami v Proxy aplikací Azure AD](application-proxy-configure-custom-domain.md)
- [Principy konektorů Proxy aplikací Azure AD](application-proxy-connectors.md)

