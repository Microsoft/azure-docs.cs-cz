---
title: Ověřování na základě záhlaví s PingAccess pro proxy aplikace služby Azure AD | Microsoft Docs
description: Publikování aplikací s PingAccess a Proxy aplikace pro podporu ověřování na základě záhlaví.
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
ms.date: 10/11/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: bb341c5700f573e61cdbbe992aa0b0eb99389b2f
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="header-based-authentication-for-single-sign-on-with-application-proxy-and-pingaccess"></a>Ověřování na základě záhlaví pro jednotné přihlašování s Proxy aplikace a PingAccess

Azure Proxy aplikace služby Active Directory a PingAccess společně společně poskytují zákazníkům Azure Active Directory přístup k i další aplikace. PingAccess rozšíří [stávající nabídky Proxy aplikace](active-directory-application-proxy-get-started.md) zahrnout jednoho přístupového přihlášení do aplikace, které používají hlavičky pro ověřování.

## <a name="what-is-pingaccess-for-azure-ad"></a>Co je PingAccess pro Azure AD?

PingAccess pro Azure Active Directory je nabídky PingAccess, která umožňuje poskytnout přístup uživatelů a jednotné přihlašování pro aplikace, které používají hlavičky pro ověřování. Proxy aplikace zpracovává tyto aplikace jako libovolný jiný, k ověření přístupu pomocí služby Azure AD a následné předání přenosy přes službu konektoru. PingAccess nachází před aplikace a přeloží tokenu přístupu z Azure AD na hlavičku a aplikace obdrží ověřování ve formátu, který může číst.

Uživatelé nebudou Všimněte si, něco jinak při přihlášení používat podnikové aplikace. Mohou i nadále pracovat z odkudkoli na libovolném zařízení. 

Vzhledem k tomu, že konektory Proxy aplikace směrovat vzdálené provoz na všechny aplikace bez ohledu na jejich typ ověřování, budete dál automaticky, také Vyrovnávání zatížení.

## <a name="how-do-i-get-access"></a>Jak získám přístup?

Vzhledem k tomu, že se tento scénář nabízí prostřednictvím partnerství mezi Azure Active Directory a PingAccess, potřebujete licence pro obě služby. Však předplatných Azure Active Directory Premium zahrnují základní PingAccess licencí, které pokrývá až 20 aplikace. Pokud potřebujete publikovat více než 20 aplikace založené na záhlaví, si můžete zakoupit další licence od PingAccess. 

Další informace najdete v článku [Edice služby Azure Active Directory](active-directory-editions.md).

## <a name="publish-your-application-in-azure"></a>Publikování aplikace v Azure

Tento článek je určený pro uživatele, kteří jsou při prvním publikování aplikace pomocí tento scénář. Provede jak začít pracovat s aplikací a PingAccess, kromě publikování kroky. Pokud jste již nakonfigurovali obě služby, ale chcete aktualizačnímu programu na publikování kroky, můžete přeskočit instalace konektoru a přesunout na [přidat aplikace do Azure AD pomocí Proxy aplikace](#add-your-app-to-Azure-AD-with-Application-Proxy).

>[!NOTE]
>Vzhledem k tomu, že tento scénář je spolupráci mezi službou Azure AD a PingAccess, některé pokyny existovat na serveru Identity příkazu Ping.

### <a name="install-an-application-proxy-connector"></a>Instalace konektoru Proxy aplikace

Pokud už máte Proxy aplikace povolena a máte nainstalovaný konektor, můžete tuto část přeskočit a přesunout na [přidat aplikace do Azure AD pomocí Proxy aplikace](#add-your-app-to-azure-ad-with-application-proxy).

Konektor Proxy aplikace je služba systému Windows Server, která přesměruje přenosy z vzdálení zaměstnanci k publikovaným aplikacím. Podrobné pokyny k instalaci, najdete v části [povolení Proxy aplikace v portálu Azure](active-directory-application-proxy-enable.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
2. Vyberte **Azure Active Directory** > **proxy aplikace**.
3. Vyberte **stáhnout konektor** zahájíte stahování konektoru Proxy aplikace. Postupujte podle pokynů pro instalaci.

   ![Povolení Proxy aplikace a stáhnout konektor](./media/application-proxy-ping-access/install-connector.png)

4. Stahování konektoru by měl automaticky povolení Proxy aplikace pro váš adresář, ale pokud ne, můžete si vybrat **povolení Proxy aplikace**.


### <a name="add-your-app-to-azure-ad-with-application-proxy"></a>Přidat aplikaci do Azure AD pomocí Proxy aplikace

Existují dvě akce, které je třeba provést na portálu Azure. Nejprve budete muset publikování aplikace pomocí Proxy aplikace. Poté která potřebujete shromáždit určité informace o aplikaci, kterou můžete použít během PingAccess kroky.

Postupujte podle těchto kroků k publikování aplikace. Pro podrobnější návod kroky 1-8, viz [publikování aplikací pomocí proxy aplikace služby Azure AD](application-proxy-publish-azure-portal.md).

1. Jestliže jste v poslední části, přihlaste se k [portál Azure](https://portal.azure.com) jako globální správce.
2. Vyberte **Azure Active Directory** > **podnikové aplikace, které**.
3. Vyberte **přidat** v horní části okna.
4. Vyberte **místní aplikace**.
5. Vyplňte požadovaná pole s informacemi o nové aplikace. Použijte následující pokyny pro nastavení:
   - **Interní adresa URL**: normálně zadejte adresu URL, kterou přejdete na přihlašovací stránce aplikace, když jste v podnikové síti. V tomto scénáři musí konektor PingAccess proxy považovat za titulní stránky aplikace. Použijte tento formát: `https://<host name of your PA server>:<port>`. Port je 3000 ve výchozím nastavení, ale můžete ji nakonfigurovat v PingAccess.

    > [!WARNING]
    > Pro tento typ jednotného přihlašování interní adresa URL musí používat protokol https a nelze používat protokol http.

   - **Metoda předběžného ověřování**: Azure Active Directory
   - **Převede adresu URL v hlavičkách**: Ne

   >[!NOTE]
   >Pokud je vaší první aplikace, použijte ke spuštění a vrátit zpět a aktualizovat toto nastavení, pokud změníte konfiguraci PingAccess port 3000. Pokud je aplikace druhý nebo novější, to bude nutné naslouchací proces, který jste nakonfigurovali v PingAccess shodovat. Další informace o [naslouchací procesy v PingAccess](https://documentation.pingidentity.com/pingaccess/pa31/index.shtml#Listeners.html).

6. Vyberte **přidat** v dolní části okna. Přidání vaší aplikace a otevře se v nabídce rychlý start.
7. V nabídce rychlý start, vyberte **přiřadit uživatele pro testování**, a přidejte alespoň jednoho uživatele k aplikaci. Ujistěte se, že tento testovací účet má přístup k aplikaci místní.
8. Vyberte **přiřadit** uložit přiřazení uživatelských testu.
9. V okně správy aplikace, vyberte **jednotného přihlašování**.
10. Zvolte **na základě záhlaví přihlašování** z rozevírací nabídky. Vyberte **Uložit**.

   >[!TIP]
   >Pokud je to na základě záhlaví jednotné přihlašování používáte poprvé, budete muset nainstalovat PingAccess. Pokud chcete mít jistotu, že se automaticky přidruží instalace PingAccess vašeho předplatného Azure, použijte odkaz na této stránce jedné přihlašování ke stažení PingAccess. Můžete otevřít web Stažení teď nebo vraťte na tuto stránku později. 

   ![Vyberte na základě záhlaví přihlášení](./media/application-proxy-ping-access/sso-header.PNG)

11. Zavřete okno aplikace Enterprise nebo posuňte se úplně levé straně si vrátit do nabídky Azure Active Directory.
12. Vyberte **Registrace aplikací**.

   ![Vyberte aplikaci registrace](./media/application-proxy-ping-access/app-registrations.png)

13. Vyberte aplikaci, které jste právě přidali, pak **adresy URL odpovědí**.

   ![Vyberte adresy URL odpovědí](./media/application-proxy-ping-access/reply-urls.png)

14. Zkontrolujte, zda externí adresu URL, který jste přiřadili do vaší aplikace v kroku 5 je v seznamu adresy URL odpovědí. Pokud není, přidejte ji.
15. V okně Nastavení aplikace vyberte **požadovaná oprávnění**.

  ![Vyberte požadovaná oprávnění](./media/application-proxy-ping-access/required-permissions.png)

16. Vyberte **Přidat**. Rozhraní API, zvolte **Windows Azure Active Directory**, pak **vyberte**. Oprávnění, zvolte **pro čtení a zápis všech aplikací** a **přihlášení a čtení profilu uživatele**, pak **vyberte** a **provádí**.  

  ![Vybrat oprávnění](./media/application-proxy-ping-access/select-permissions.png)

17. Před zavřením obrazovce oprávnění udělení oprávnění. 
![Udělení oprávnění](media/application-proxy-ping-access/grantperms.png)

### <a name="collect-information-for-the-pingaccess-steps"></a>Shromažďovat informace o krocích PingAccess

1. V okně nastavení vaší aplikace, vyberte **vlastnosti**. 

  ![Vyberte vlastnosti](./media/application-proxy-ping-access/properties.png)

2. Uložit **Id aplikace** hodnotu. Používá se pro ID klienta při konfiguraci PingAccess.
3. V okně Nastavení aplikace vyberte **klíče**.

  ![Vyberte klíče](./media/application-proxy-ping-access/Keys.png)

4. Vytvořte klíč zadáním klíče popis a v rozevírací nabídce zvolíte datum vypršení platnosti.
5. Vyberte **Uložit**. Identifikátor GUID se zobrazí v **hodnotu** pole.

  Nyní, uložte tuto hodnotu jako nebudete moci zobrazit znovu po zavření tohoto okna.

  ![Vytvořit nový klíč](./media/application-proxy-ping-access/create-keys.png)

6. Zavřete okno registrace aplikace nebo posuňte se úplně levé straně si vrátit do nabídky Azure Active Directory.
7. Vyberte **vlastnosti**.
8. Uložit **ID adresáře** identifikátor GUID.

### <a name="optional---update-graphapi-to-send-custom-fields"></a>Volitelné – aktualizace GraphAPI odeslat vlastní pole

Seznam tokeny zabezpečení, které odesílá Azure AD pro ověřování najdete v tématu [odkaz tokenu Azure AD](./develop/active-directory-token-and-claims.md). Pokud potřebujete vlastní deklarace identity, který odesílá dalších tokenů, graf můžete pomocí Průzkumníka nebo manifest pro aplikaci na portálu Azure nastavte hodnotu pole aplikace *acceptMappedClaims* k **True**.    

Tento příklad používá Explorer grafu:

```
PATCH https://graph.windows.net/myorganization/applications/<object_id_GUID_of_your_application> 

{
  "acceptMappedClaims":true
}
```
Tento příklad používá [portál Azure](https://portal.azure.com) k udpate *acceptedMappedClaims* pole:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
2. Vyberte **Azure Active Directory** > **registrace aplikace**.
3. Vyberte svou aplikaci > **Manifest**.
4. Vyberte **upravit**, vyhledejte *acceptedMappedClaims* pole a změňte hodnotu na **true**.
![Manifest aplikace.](media/application-proxy-ping-access/application-proxy-ping-access-manifest.PNG)
1. Vyberte **Uložit**.

>[!NOTE]
>Pokud chcete používat vlastní deklarace identity, musí mít také vlastní zásady definované a přiřazené k aplikaci.  Tyto zásady by měly obsahovat všechny požadované vlastní atributy.
>
>Definice zásady a přiřazení lze provést pomocí prostředí PowerShell, Azure AD Graph Explorer nebo MS Graph.  Chcete to provést v prostředí PowerShell, budete muset nejdřív `New-AzureADPolicy `a přiřaďte ho do aplikace s `Set-AzureADServicePrincipalPolicy`.  Další informace najdete v článku [dokumentaci k Azure AD zásadám](active-directory-claims-mapping.md#claims-mapping-policy-assignment).

### <a name="optional---use-a-custom-claim"></a>Volitelné – použití vlastních deklarací identity
Chcete-li aplikace pomocí vlastních deklarací identity a zahrnout další pole, ujistěte se, že máte také [vytvořili vlastní deklarace mapování zásad a přiřazené k aplikaci](active-directory-claims-mapping.md#claims-mapping-policy-assignment).

## <a name="download-pingaccess-and-configure-your-app"></a>Stáhnout PingAccess a konfigurace aplikace

Teď, když jste dokončili všechny kroky instalace služby Azure Active Directory, můžete přesunout ke konfiguraci PingAccess. 

Podrobné kroky pro PingAccess součástí tohoto scénáře pokračovat v dokumentaci příkazu Ping Identity [PingAccess nakonfigurovat pro Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html).

Tyto kroky vás provedou procesem získávání PingAccess účtu, pokud jste ještě nemáte, instalace serveru PingAccess a vytvoření připojení k Azure AD OIDC zprostředkovatele s ID adresáře, který jste zkopírovali z portálu Azure. Potom hodnoty ID aplikace a klíč použijete k vytvoření relace webu na PingAccess. Potom můžete nastavit mapování identit a vytvořit virtuální hostitele, webu nebo aplikace.

### <a name="test-your-app"></a>Testování aplikace

Po dokončení těchto kroků, musí být aplikace spuštěná. Chcete-li otestovat ji, otevřete prohlížeč a přejděte na externí adresu URL, kterou jste vytvořili při publikování aplikace v Azure. Přihlaste se pomocí účtu test, který jste přiřadili k aplikaci.

## <a name="next-steps"></a>Další postup

- [Konfigurace PingAccess pro Azure AD](https://docs.pingidentity.com/bundle/paaad_m_ConfigurePAforMSAzureADSolution_paaad43/page/pa_c_PAAzureSolutionOverview.html)
- [Jak Azure AD Application Proxy poskytovat jednotné přihlašování?](application-proxy-sso-overview.md)
- [Řešení potíží s Proxy aplikace](active-directory-application-proxy-troubleshoot.md)
