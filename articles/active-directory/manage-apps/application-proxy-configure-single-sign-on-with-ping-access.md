---
title: Ověřování založené na hlavičkách pingaccessu pro Proxy aplikací Azure AD | Dokumentace Microsoftu
description: Publikování aplikací pomocí Proxy aplikace a PingAccess pro podporu ověřování založené na hlavičkách.
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
ms.date: 10/11/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 53d613052713e5144cb9d96aeeb937392fd4736a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902922"
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Ověřování založené na hlavičkách pro jednotné přihlašování s Proxy aplikací a Pingaccessem

Azure Active Directory Application Proxy a PingAccess uzavřeli partnerství s společně poskytnout přístup k i další aplikace Azure Active Directory zákazníkům. PingAccess rozšíří [stávající nabídky Proxy aplikací](application-proxy.md) zahrnout přístup jednotné přihlašování k aplikacím, které k ověřování používají hlavičky.

## <a name="what-is-pingaccess-for-azure-ad"></a>Co je PingAccess pro Azure AD?

PingAccess pro Azure Active Directory je nabídka služby PingAccess, který vám umožní poskytnout uživatelům přístup a jednotné přihlašování k aplikacím, které k ověřování používají hlavičky. Proxy aplikací bude tyto aplikace stejně jako jakýkoli jiný, k ověření přístupu pomocí Azure AD a následným předáním provoz přes službu konektoru. PingAccess je umístěný před aplikace a převádí přístupového tokenu z Azure AD na záhlaví tak, aby aplikace obdrží ověřování ve formátu, který může číst.

Uživatelé nebudou Všimněte si, že něco jinak při přihlášení používat podnikové aplikace. Mohou i nadále pracovat z kdekoli na jakémkoli zařízení. 

Vzhledem k tomu, že konektory Proxy aplikací přímo vzdálenou komunikaci pro všechny aplikace bez ohledu na jejich typ ověřování, budou dál automaticky, také vyrovnávat zatížení.

## <a name="how-do-i-get-access"></a>Jak získám přístup?

Vzhledem k tomu, že tento scénář je dostupná prostřednictvím Azure Active Directory a PingAccess partnerství, potřebují licence pro obě služby. Předplatná Azure Active Directory Premium však obsahují PingAccess licence basic, která zahrnuje až 20 aplikací. Pokud je potřeba publikovat více než 20 aplikací založené na hlavičkách, si můžete zakoupit další licence od PingAccess. 

Další informace najdete v článku [Edice služby Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="publish-your-application-in-azure"></a>Publikování aplikace v Azure

Tento článek je určený pro uživatele, kteří jsou poprvé publikování aplikace v tomto scénáři. Provede jak začít pracovat s aplikací a Pingaccessem, kromě publikování kroky. Pokud jste už nakonfigurovali obě služby, ale chcete si můžete znovu projít postup publikování, můžete přeskočit instalaci konektoru a přejděte na [přidání aplikace do Azure AD pomocí Proxy aplikace](#add-your-app-to-Azure-AD-with-Application-Proxy).

>[!NOTE]
>Vzhledem k tomu, že tento scénář je partnerství mezi službami Azure AD a PingAccess, některé pokyny existují na webu s Ping Identity.

### <a name="install-an-application-proxy-connector"></a>Nainstalujte konektor Proxy aplikací

Pokud už máte Proxy aplikací byl povolen a mají nainstalovaný konektor, můžete tuto část přeskočte a přejděte na [přidání aplikace do Azure AD pomocí Proxy aplikace](#add-your-app-to-azure-ad-with-application-proxy).

Konektor Proxy aplikací je služba Windows Server, který směruje provoz z vzdálení zaměstnanci do publikované aplikace. Podrobnější pokyny k instalaci najdete v článku [povolení Proxy aplikace na webu Azure Portal](application-proxy-enable.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
2. Vyberte **Azure Active Directory** > **proxy aplikací**.
3. Vyberte **stáhnout konektor** spustit stažení konektoru Proxy aplikací. Postupujte podle pokynů k instalaci.

   ![Povolit Proxy aplikací a stáhnout konektor](./media/application-proxy-configure-single-sign-on-with-ping-access/install-connector.png)

4. Stažení konektoru by měl automaticky povolí Proxy aplikace pro svůj adresář, ale pokud ne, můžete si vybrat **povolení Proxy aplikace**.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Přidání aplikace do Azure AD pomocí Proxy aplikace

Existují dvě akce, které musíte provést na webu Azure Portal. Je třeba nejprve, můžete publikovat svoji aplikaci pomocí Proxy aplikace. Potom budete muset shromažďovat některé informace o aplikaci, kterou můžete použít během kroků PingAccess.

Postupujte podle těchto kroků a publikujte svou aplikaci. Pro podrobnější návod k kroky 1-8, viz [publikování aplikací pomocí Proxy aplikací Azure AD](application-proxy-publish-azure-portal.md).

1. Pokud jste v poslední části, přihlaste se k [webu Azure portal](https://portal.azure.com) jako globální správce.
2. Vyberte **Azure Active Directory** > **podnikové aplikace**.
3. Vyberte **přidat** v horní části okna.
4. Vyberte **On-premises application**.
5. Vyplňte požadovaná pole s informacemi o novou aplikaci. Použijte následující pokyny pro nastavení:
   - **Interní adresa URL**: obvykle zadejte adresu URL, která vás na přihlašovací stránce aplikace, když jste v podnikové síti. V tomto scénáři konektoru je potřeba považovat za PingAccess proxy serveru na přední stránce aplikace. Použijte tento formát: `https://<host name of your PA server>:<port>`. Port, který je 3000 ve výchozím nastavení, ale můžete ho nakonfigurovat PingAccess.

    > [!WARNING]
    > Pro tento typ jednotného přihlašování interní adresa URL musí používat protokol https a http nelze použít.

   - **Metoda předběžného ověřování služby**: Azure Active Directory
   - **Přeložit adresy URL v hlavičkách**: Ne

   >[!NOTE]
   >Pokud je toto první aplikace, použijte ke spuštění a vrátit zpět a aktualizovat toto nastavení, pokud změníte konfiguraci PingAccess port 3000. Pokud vaše aplikace druhý nebo novější, bude nutné tak, aby odpovídaly naslouchací proces, který jste nakonfigurovali v PingAccess. Další informace o [naslouchacích procesů v PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Vyberte **přidat** v dolní části okna. Přidá se vaše aplikace a otevře se nabídka rychlý start.
7. V nabídce rychlý start vyberte **přiřadit uživatele pro testování**, a přidejte aspoň jednoho uživatele k aplikaci. Ujistěte se, že testovacího účtu má přístup k místní aplikaci.
8. Vyberte **přiřadit** se uložit přiřazení uživatelských testu.
9. V okně správy aplikace vyberte **jednotného přihlašování**.
10. Zvolte **přihlašování na základě záhlaví** z rozevírací nabídky. Vyberte **Uložit**.

   >[!TIP]
   >Pokud je toto vaše první přihlášení pomocí založeným na hlavičkách jednotného přihlašování, budete muset nainstalovat PingAccess. Ujistěte se, že vaše předplatné Azure je automaticky přiřazen k instalaci PingAccess, použijte odkaz na této stránce jednotné přihlašování ke stažení PingAccess. Můžete teď otevřít server pro stahování nebo vraťte na tuto stránku později. 

   ![Vyberte přihlašování na základě záhlaví](./media/application-proxy-configure-single-sign-on-with-ping-access/sso-header.PNG)

11. Zavřete okno podnikových aplikací nebo posuňte se úplně vlevo se vraťte do nabídky Azure Active Directory.
12. Vyberte **Registrace aplikací**.

   ![Výběr registrace aplikací](./media/application-proxy-configure-single-sign-on-with-ping-access/app-registrations.png)

13. Vyberte aplikace, které jste právě přidali, pak **adresy URL odpovědí**.

   ![Vyberte adresy URL odpovědí](./media/application-proxy-configure-single-sign-on-with-ping-access/reply-urls.png)

14. Zaškrtněte, pokud chcete zjistit, jestli externí adresu URL, který jste přiřadili do vaší aplikace v kroku 5 v seznamu adres URL odpovědí. Pokud není, přidejte ji.
15. V okně Nastavení app vyberte **požadovaná oprávnění**.

  ![Vyberte požadovaná oprávnění](./media/application-proxy-configure-single-sign-on-with-ping-access/required-permissions.png)

16. Vyberte **Přidat**. Rozhraní API, zvolte **Windows Azure Active Directory**, pak **vyberte**. Oprávnění, zvolte **pro čtení a zápis všech aplikací** a **přihlášení a čtení profilu uživatele**, pak **vyberte** a **provádí**.  

  ![Vybrat oprávnění](./media/application-proxy-configure-single-sign-on-with-ping-access/select-permissions.png)

17. Před zavřením okna oprávnění udělit oprávnění. 
![Udělení oprávnění](./media/application-proxy-configure-single-sign-on-with-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Shromáždit data za PingAccess kroky

1. V okně Nastavení aplikace, vyberte **vlastnosti**. 

  ![Výběr vlastností](./media/application-proxy-configure-single-sign-on-with-ping-access/properties.png)

2. Uložit **Id aplikace** hodnotu. Používá se pro ID klienta při konfiguraci PingAccess.
3. V okně Nastavení app vyberte **klíče**.

  ![Vyberte klíče](./media/application-proxy-configure-single-sign-on-with-ping-access/Keys.png)

4. Vytvořte klíč tak, že zadáte popis klíče a zvolíte datum vypršení platnosti z rozevírací nabídky.
5. Vyberte **Uložit**. Identifikátor GUID se zobrazí v **hodnotu** pole.

  Uložte tuto hodnotu, nebudou moct podívat, jak to znovu po zavření tohoto okna.

  ![Vytvořit nový klíč](./media/application-proxy-configure-single-sign-on-with-ping-access/create-keys.png)

6. Zavřete okno registrace aplikace a posuňte se úplně vlevo se vraťte do nabídky Azure Active Directory.
7. Vyberte **vlastnosti**.
8. Uložit **ID adresáře** identifikátor GUID.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Nepovinné - aktualizace GraphAPI k odesílání vlastních polí

Seznam tokeny zabezpečení, které se odešle službě Azure AD pro ověřování najdete v tématu [odkaz tokenu Azure AD](../develop/v1-id-and-access-tokens.md). Pokud potřebujete vlastní deklarace identity, která odesílá další tokeny, pomocí Graph Exploreru nebo v manifestu pro aplikaci na webu Azure Portal nastavit pole aplikace *acceptMappedClaims* k **True**.    

Tento příklad používá Graph Exploreru:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
V tomto příkladu [webu Azure portal](https://portal.azure.com) k zjistit *acceptedMappedClaims* pole:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
2. Vyberte **Azure Active Directory** > **registrace aplikací**.
3. Vyberte svou aplikaci > **Manifest**.
4. Vyberte **upravit**, vyhledejte *acceptedMappedClaims* pole a změňte hodnotu na **true**.
![Manifest aplikace](./media/application-proxy-configure-single-sign-on-with-ping-access/application-proxy-ping-access-manifest.PNG)
1. Vyberte **Uložit**.

>[!NOTE]
>Pokud chcete použít vlastní deklarace identity, musí mít také vlastní zásady definované a přiřazené k aplikaci.  Tato zásada by měl obsahovat všechny požadované vlastní atributy.
>
>Definice zásady a přiřazení to provést prostřednictvím Powershellu, Azure AD Graph Exploreru nebo MS Graphu.  Pokud se to v prostředí PowerShell, budete muset nejdřív použít `New-AzureADPolicy `a přiřaďte ho k aplikaci pomocí `Set-AzureADServicePrincipalPolicy`.  Další informace najdete v článku [dokumentace ke službě Azure AD Policy](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

### <a name="optional---use-a-custom-claim"></a>Nepovinné – použití vlastní deklarace identity
Chcete-li aplikaci pomocí vlastní deklarace identity a zahrnutí dalších polí, ujistěte se, že máte také [vytvoření vlastní deklarace identity mapování zásad a přiřazené k aplikaci](../develop/active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-app"></a>Stáhněte si PingAccess a konfigurace aplikace

Teď, když jste dokončili všechny kroky instalace služby Azure Active Directory, které můžete přejít ke konfiguraci PingAccess. 

Podrobné pokyny k PingAccess součástí tohoto scénáře pokračovat v dokumentaci Ping Identity [nakonfigurovat PingAccess pro Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Tyto kroky vás provedou procesem získávání PingAccess účtu, pokud ho ještě nemáte, instalace serveru PingAccess a vytvoření připojení k Azure AD OIDC zprostředkovatele s ID adresáře, který jste zkopírovali z portálu Azure portal. Hodnoty ID aplikace a klíč potom použijete k vytvoření relace webové na PingAccess. Potom můžete nastavit mapování identit a vytvořit virtuální hostitel, webu nebo aplikace.

### <a name="test-your-app"></a>Testování aplikace

Po dokončení těchto kroků, by vaše aplikace do provozu. K otestování, otevřete prohlížeč a přejděte na externí adresu URL, kterou jste vytvořili při publikování aplikace v Azure. Přihlaste se pomocí testovacího účtu, který jste přiřadili do aplikace.

## <a name="next-steps"></a>Další postup

- [Nakonfigurovat PingAccess pro Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Jak Proxy aplikací Azure AD poskytuje jednotné přihlašování?](application-proxy-single-sign-on.md)
- [Poradce při potížích s Proxy aplikací](application-proxy-troubleshoot.md)
