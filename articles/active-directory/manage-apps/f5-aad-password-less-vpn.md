---
title: Zabezpečený hybridní přístup k Azure AD pomocí F5 VPN | Microsoft Docs
description: Kurz pro Azure Active Directory integraci jednotného přihlašování s nástrojem F5 BIG-IP pro připojení VPN bez hesla
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84e177f1ce55d803f54bb2553078441557e5c191
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730883"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>Kurz pro Azure Active Directory integraci s jednotným přihlašováním pomocí F5 BIG-IP pro heslo bez hesla – bez VPN

V tomto kurzu se dozvíte, jak integrovat řešení F5's s využitím standardu SSL (Secure Socket Layer) založeného na velkém protokolu IP pomocí služby Azure Active Directory (AD) pro zabezpečený hybridní přístup (SHA).

Integrace služby BIG-IP SSL-VPN s Azure AD poskytuje [mnoho klíčových výhod](f5-aad-integration.md), včetně:

- Vylepšené řízení nulové důvěryhodnosti prostřednictvím předběžného [ověřování a autorizace Azure AD](../../app-service/overview-authentication-authorization.md)

- [Ověřování bez hesla u služby VPN](https://www.microsoft.com/security/business/identity/passwordless)

- Správa identit a přístupu z jedné roviny ovládacího prvku – [Azure Portal](https://portal.azure.com/#home)

I když tato Skvělé hodnota připadá, klasická síť VPN ale zůstane v predikátech k vystavení síťového obvodu, kde Trusted je na vnitřním a nedůvěryhodném externím místě. Tento model již neplatí pro dosažení skutečného stav nulové důvěryhodnosti, protože firemní prostředky již nejsou omezeny na zdi podnikového datového centra, ale ne napříč více cloudovým prostředím bez pevných hranic. Z tohoto důvodu doporučujeme našim zákazníkům zvážit přechod na další identitu řízený přístup na [základě správy přístupu na jednotlivé aplikace](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md).

## <a name="scenario-description"></a>Popis scénáře

V tomto scénáři bude instance BIG-IP APM služby SSL-VPN nakonfigurovaná jako poskytovatel služeb SAML (SP) a služba Azure AD se bude jednat o důvěryhodný IDP SAML, který poskytuje předběžné ověření. Jednotné přihlašování (SSO) ze služby Azure AD je pak k dispozici prostřednictvím ověřování založeného na deklaracích identity APM na základě deklarace identity, které zajišťuje bezproblémové prostředí pro přístup k síti VPN.

![Obrázek ukazuje architekturu SSL-VPN](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>Všechny ukázkové řetězce nebo hodnoty, na které se odkazuje v rámci této příručky, by se měly nahradit hodnotami pro vaše skutečné prostředí.

## <a name="prerequisites"></a>Požadavky

Nemusíte ale mít k řadu nebo znalosti velkých IP adres F5, budete potřebovat:

- [Bezplatné předplatné](https://azure.microsoft.com/trial/get-started-active-directory/) Azure AD nebo novější

- Identity uživatelů by se měly [synchronizovat z místního adresáře](../hybrid/how-to-connect-sync-whatis.md) do Azure AD.

- Účet s [oprávněními](../roles/permissions-reference.md#application-administrator) správce aplikace Azure AD

- Stávající infrastruktura BIG-IP s směrováním klientského provozu do a z velké IP adresy nebo [nasazení virtuální edice Big-IP do Azure](f5-bigip-deployment-guide.md).

- Záznam pro službu sítě VPN s podporou BIG-IP bude muset existovat ve veřejném DNS nebo v souboru localhost testovacího klienta při testování.

- Služba BIG-IP by měla být zřízená s potřebnými certifikáty SSL pro publikování služeb přes protokol HTTPS.

Familiarizing s využitím nástroje [F5 Big-IP terminologie](https://www.f5.com/services/resources/glossary) také pomůže pochopit různé komponenty, na které se odkazuje v průběhu tohoto kurzu.

>[!NOTE]
>Azure se neustále vyvíjí, takže nemusíte být překvapeni, pokud najdete jakékoli drobné odlišnosti mezi pokyny v této příručce a co vidíte v Azure Portal. Snímky obrazovky jsou ale z v15 s velkým objemem IP adres, ale zůstávají relativně podobné jako v 13.1.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Přidat F5 BIG-IP z Galerie Azure AD

Nastavení důvěryhodnosti federace SAML mezi velkou IP adresou umožňuje, aby služba Azure AD BIG-IP před udělením přístupu k publikované službě sítě VPN mohla předat předběžné ověřování a [podmíněný přístup](../conditional-access/overview.md) k Azure AD.

1. Přihlaste se k portálu Azure AD pomocí účtu s právy správce aplikací.

2. V levém navigačním podokně vyberte **službu Azure Active Directory** .

3. Přejít na **podnikové aplikace** a na horním pásu karet vyberte **Nová aplikace**.

4. Vyhledejte F5 v galerii a vyberte **F5 služba Azure AD Integration APM Big-IP**.

5. Zadejte název aplikace a potom **přidejte nebo vytvořte** , aby se přidala do vašeho tenanta. Uživatel může název zobrazit jako ikonu v portálech aplikací Azure a Office 365. Název by měl odrážet konkrétní službu. Například VPN.

## <a name="configure-azure-ad-sso"></a>Konfigurace jednotného přihlašování v Azure AD

1. Nové vlastnosti aplikace F5 v zobrazení najdete na webu **Správa**  >  **jednotného přihlašování** .

2. Na stránce **Vyberte metodu jednotného přihlašování** vyberte **SAML**. Po zobrazení výzvy k uložení nastavení jednotného přihlašování vyberte **Ne, uloží se později**.

3. V nabídce **nastavit jednotné přihlašování pomocí SAML** vyberte ikonu pera pro **základní konfiguraci SAML** , abyste zadali následující podrobnosti:

   - Nahraďte předem definovanou **adresu URL identifikátoru** adresou URL vaší publikované služby Big-IP. Například `https://ssl-vpn.contoso.com`.

   - Udělejte to stejně jako v textovém poli **Adresa URL odpovědi** , včetně cesty koncového bodu SAML. Například `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`.

   - V této konfiguraci se aplikace jenom v režimu iniciované IDP, kde Azure AD před přesměrováním na službu SAML ve velkém rozhraní SAML vystaví uživateli s kontrolním výrazem SAML. U aplikací, které nepodporují režim iniciované IDP, zadejte **přihlašovací adresu URL** pro službu Big-IP SAML. Například `https://ssl-vpn.contoso.com`.

   - V poli Adresa URL pro odhlášení Zadejte koncový bod služby Single-IP APM Single logout (SLO), který se předoznačené jako nedokončené pomocí hlavičky hostitele publikované služby. Například `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`.

   Když zadáte adresu URL pro SLO, zajistíte ukončení uživatelské relace na obou koncích, BIG-IP a Azure AD, až se uživatel odhlásí. Technologie APM pro velké IP adresy taky nabízí [možnost](https://support.f5.com/csp/article/K12056) ukončení všech relací při volání konkrétní adresy URL aplikace.

![Obrázek ukazuje základní konfiguraci SAML](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>Z TMOS v16 koncový bod SAML SLO se změnil na/SAML/SP/Profile/redirect/slo

4. Před ukončením nabídky konfigurace SAML vyberte **Uložit** a vynechejte výzvu k ověření jednotného přihlašování.

Podívejte se na téma vlastnosti **atributů uživatele & deklarací identity** , protože služba Azure AD je vydává uživatelům pro ověřování s funkcí APM ve velkém protokolu IP.

![Image zobrazuje deklarace atributů uživatele](media/f5-sso-vpn/user-attributes-claims.png)

Nemusíte mít k dispozici žádné další konkrétní deklarace, které vaše služba publikování velkých IP adres očekává, a současně se zaznamená, že všechny deklarace definované kromě výchozí sady se vystavují jenom v případě, že existují v Azure AD, jako vyplněné atributy. Stejným způsobem musí taky role adresáře [nebo](../hybrid/how-to-connect-fed-group-claims.md) členství ve skupině definovat i pro objekt uživatele v Azure AD, aby se mohly vydávat jako deklarace identity.

![Obrázek zobrazuje odkaz na stažení federačních metadat](media/f5-sso-vpn/saml-signing-certificate.png)

Podpisové certifikáty SAML vytvořené službou Azure AD mají životnost po dobu tří let, takže budete muset spravovat pomocí pokynů publikovaných v Azure AD.

### <a name="azure-ad-authorization"></a>Autorizace Azure AD

Ve výchozím nastavení bude služba Azure AD vydávat tokeny jenom uživatelům, kterým byl udělen přístup ke službě.

1. Pořád v zobrazení konfigurace aplikace vyberte **Uživatelé a skupiny** .

2. Vyberte **+ Přidat uživatele** a v nabídce přidat přiřazení vyberte **Uživatelé a skupiny** .

3. V dialogovém okně **Uživatelé a skupiny** přidejte skupiny uživatelů, kteří mají oprávnění k přístupu k síti VPN, a potom **Vyberte**  >  **přiřadit** .

![Obrázek ukazuje přidání odkazu na uživatele ](media/f5-sso-vpn/add-user-link.png)

4. Tím se dokončí část Azure AD vztahu důvěryhodnosti federace SAML. Pro službu BIG-IP APM se teď dá nastavit publikování služby SSL-VPN a nakonfigurované pomocí odpovídající sady vlastností pro dokončení vztahu důvěryhodnosti pro předběžné ověření SAML.

## <a name="big-ip-apm-configuration"></a>Konfigurace pro BIG-IP APM

### <a name="saml-federation"></a>Federace SAML

V následující části je vytvořen poskytovatel služby BIG-IP SAML a odpovídající objekty IDP SAML, které jsou nutné k dokončení federování služby VPN pomocí Azure AD.

1. Přejděte na **přístup** k  >  **federaci**  >  **poskytovatel služeb SAML**  >  **místní služby SP** a vyberte **vytvořit** .

![Obrázek ukazuje konfiguraci BIG-IP SAML.](media/f5-sso-vpn/bigip-saml-configuration.png)

2. Zadejte **název** a stejnou hodnotu **ID entity** , kterou jste dříve definovali v Azure AD, a plně kvalifikovaný název domény hostitele, který se použije pro připojení k aplikaci.

![Obrázek ukazuje vytvoření nové služby SAML SP](media/f5-sso-vpn/create-new-saml-sp.png)

   Nastavení **názvu** SP se vyžaduje jenom v případě, že ID entity není přesná shoda části názvu hostitele publikované adresy URL, nebo pokud není v běžném formátu adresy URL založeného na názvu hostitele. Zadejte externí schéma a název hostitele publikované aplikace, pokud je ID entity `urn:ssl-vpn:contosoonline` .

3. Posuňte se dolů a vyberte nový **objekt SAML SP** a vyberte **BIND/odpojte konektory IDP**.

![Obrázek ukazuje vytvoření federace pomocí místní služby SP](media/f5-sso-vpn/federation-local-sp-service.png)

4. Vyberte **vytvořit nový konektor IDP** a z rozevírací nabídky vyberte **z metadat** .

![Obrázek ukazuje vytvoření nového konektoru IDP](media/f5-sso-vpn/create-new-idp-connector.png)

5. Přejděte do souboru XML federačních metadat, který jste stáhli dříve, a zadejte **název zprostředkovatele identity** pro objekt APM, který bude představovat externí IDP SAML.

6. Vyberte **Přidat nový řádek** a vyberte nový konektor Azure AD External IDP.

![Obrázek ukazuje externí konektor IDP](media/f5-sso-vpn/external-idp-connector.png)

7. Vyberte **aktualizovat** , pokud chcete vytvořit objekt SAML SP s objektem IDP SAML, a pak vyberte **OK**.

![Obrázek ukazuje IDP SAML pomocí SP](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Konfigurace Webtop

Následující postup umožní uživatelům nabízení SSL-VPN nabídnout uživatelům prostřednictvím vlastního webového portálu BIG-IP.

1. Přejděte do části **přístup**  >  **k**  >  **seznamům webWebtop seznamů** a vyberte **vytvořit**.

2. Dejte portálu název a nastavte typ na **úplný**. Například `Contoso_webtop`.

3. Upravte zbývající předvolby a pak vyberte **dokončeno**.

![Obrázek ukazuje Webtop konfiguraci](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>Konfigurace sítě VPN

Funkce sítě VPN se skládá z několika prvků, z nichž každá řídí jiný aspekt celkové služby.

1. Přejděte na **přístup přístup** k  >  síti **VPN nebo**  >  k fondu zapůjčení IPv4 **(VPN Network Access)**  >   a vyberte **vytvořit**.

2. Zadejte název fondu IP adres přidělených klientům VPN. Například Contoso_vpn_pool

3. Nastavte typ na **Rozsah IP adres** a zadejte počáteční a koncovou IP adresu a potom klikněte na **Přidat** a **Dokončit**.

![Obrázek znázorňuje konfiguraci sítě VPN.](media/f5-sso-vpn/vpn-configuration.png)

Seznam přístupu k síti zřídí službu s nastavením IP adresy a DNS z fondu VPN, oprávnění směrování uživatelů a může v případě potřeby také spouštět aplikace.

1. Přejděte na **přístup**  >  **připojení/VPN:**  >  **seznamy přístup k síti** (VPN) a vyberte **vytvořit**.

2. Zadejte název seznamu a titulku přístupu k síti VPN, například contoso-VPN následovaný **dokončeným**.

![Obrázek zobrazuje konfiguraci sítě VPN v seznamu přístup k síti](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. Na horním pásu karet vyberte **nastavení sítě** a přidejte následující nastavení:

   • **Podporovaná verze protokolu IP**: IPv4

   • **Fond zapůjčení IPv4**: Vyberte fond VPN, který jste vytvořili dřív, například Contoso_vpn_pool

![Obrázek zobrazuje fond VPN contoso.](media/f5-sso-vpn/contoso-vpn-pool.png)

   Možnosti nastavení klienta lze použít k vykonání omezení způsobu směrování klientského provozu při navázání sítě VPN.

4. Vyberte **dokončeno** a pak na kartu DNS/hostitelé přidejte nastavení:

   • **Primární názvový server IPv4**: IP adresa serveru DNS vašeho prostředí

   • **Výchozí přípona domény DNS**: přípona domény pro toto konkrétní připojení k síti VPN. Například contoso.com

![Obrázek ukazuje výchozí příponu domény](media/f5-sso-vpn/domain-suffix.png)

[Článek F5](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) poskytuje podrobné informace o úpravách zbývajících nastavení podle vašich preferencí.

K nakonfigurování nastavení pro každý typ klienta VPN, který musí služba VPN podporovat, se teď vyžaduje profil připojení BIG-IP. Například Windows, OSX a Android.

1. Přejděte na **přístup**  >  **připojení/** profily připojení k síti VPN  >    >   a vyberte **Přidat**.

2. Zadejte název profilu a nastavte nadřazený profil na **/Common/Connectivity**, například Contoso_VPN_Profile.

![Obrázek ukazuje vytvořit nový profil připojení.](media/f5-sso-vpn/create-connectivity-profile.png)

[Dokumentace k](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) F5's poskytuje další podrobnosti o podpoře klientů.

## <a name="access-profile-configuration"></a>Konfigurace profilu přístupu

U nakonfigurovaných objektů sítě VPN se vyžaduje zásada přístupu, která povoluje službu pro ověřování SAML.

1. Přejděte na **přístup**  >  **profily/zásady**  >  **přístup k profilům (zásady pro jednotlivé relace)** a vyberte **vytvořit** .

2. Zadejte název profilu a pro typ profilu vyberte **vše**, například Contoso_network_access

3. Posuňte se dolů a přidejte alespoň jeden jazyk do seznamu **přijaté jazyky** a vyberte **dokončeno** .

![Obrázek zobrazuje obecné vlastnosti](media/f5-sso-vpn/general-properties.png)

4. V poli Per-Session zásady v novém profilu přístupu vyberte **Upravit** , aby se Editor vizuálních zásad spouštěl na samostatné kartě prohlížeče.

![Obrázek ukazuje zásady vázané na relaci](media/f5-sso-vpn/per-session-policy.png)

5. Vyberte **+** znaménko a v rozevíracím seznamu vyberte **ověřování**  >  **SAML auth**  >  **Add Item**.

6. V konfiguraci ověřování pomocí protokolu SAML vyberte objekt VPN SAML SP, který jste vytvořili dříve, a pak klikněte na **Uložit**.

![Obrázek ukazuje ověřování SAML](media/f5-sso-vpn/saml-authentication.png)

7. Vyberte **+** pro úspěšnou větev ověřování SAML.

8. Na kartě přiřazení vyberte **Rozšířené přiřazení prostředku** a potom **Přidat položku** .

![Obrázek ukazuje přiřazení předběžného prostředku](media/f5-sso-vpn/advance-resource-assign.png)

9. V automaticky otevíraném okně vyberte **nový záznam** a pak **Přidat/odstranit**.

10. V podřízeném okně vyberte možnost **přístup k síti** a potom vyberte profil přístupu k síti, který jste vytvořili dříve.

![Obrázek ukazuje přidání nové položky přístupu k síti](media/f5-sso-vpn/add-new-entry.png)

11. Přepněte na kartu **Webtop** a přidejte objekt Webtop, který jste vytvořili dříve.

![Obrázek ukazuje přidání objektu Webtop](media/f5-sso-vpn/add-webtop-object.png)

12. Vyberte **Update** a potom **Save (Uložit**).

13. Vyberte odkaz v horním poli odepřít a změňte úspěšnou větev tak, aby **umožňovala** **uložení**.

![Obrázek ukazuje nový editor vizuálních zásad](media/f5-sso-vpn/vizual-policy-editor.png)

14. Tato nastavení potvrďte výběrem možnosti **použít zásady přístupu** a zavřete kartu Editor vizuálních zásad.

![Obrázek zobrazuje nového správce zásad přístupu](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>Publikování služby VPN

Když se zapojí všechna nastavení, APM teď vyžaduje front-end virtuální server, aby naslouchal klientům, kteří se připojují k síti VPN.

1. Vyberte seznam virtuálních serverů **místních přenosů** virtuálního  >    >  **serveru** a vyberte **vytvořit**.

2. Zadejte **název** virtuálního serveru VPN, například **VPN_Listener**.

3. Poskytněte virtuálnímu serveru nepoužitou **cílovou adresu IP** , která má místo pro příjem klientského provozu.

4. Nastavte port služby na **443 HTTPS** a ujistěte se, že se stav zobrazuje **povoleno** .

![Obrázek ukazuje nový virtuální server](media/f5-sso-vpn/new-virtual-server.png)

5. Nastavte **profil http** na http a přidejte profil SSL (klienta) pro veřejný certifikát SSL, který jste zřídili v rámci požadavků.

![Obrázek zobrazuje profil SSL](media/f5-sso-vpn/ssl-profile.png)

6. V části zásady přístupu nastavte **profil přístupu** a **profil připojení** tak, aby POUŽÍVALY vytvořené objekty sítě VPN.

![Obrázek znázorňuje zásady přístupu.](media/f5-sso-vpn/access-policy.png)

7. Po dokončení vyberte **dokončeno** .

8.  Vaše služba SSL-VPN je teď publikovaná a přístupná přes SHA, a to buď přímo přes adresu URL, nebo prostřednictvím portálů aplikací od Microsoftu.

## <a name="additional-resources"></a>Další zdroje informací

- [Konec hesel, přejít bez hesla](https://www.microsoft.com/security/business/identity/passwordless)

- [Co je podmíněný přístup?](../conditional-access/overview.md)

- [Microsoft Zero Trust Framework umožňující vzdálenou práci](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Pět kroků pro úplnou integraci aplikací s Azure AD](../fundamentals/five-steps-to-full-application-integration-with-azure-ad.md)

## <a name="next-steps"></a>Další kroky

Otevřete prohlížeč na vzdáleném klientském počítači se systémem Windows a přejděte na adresu URL **služby Big-IP VPN**. Po ověření ve službě Azure AD se zobrazí portál BIG-IP Webtop a síť VPN.

![Obrázek znázorňuje spouštěč sítě VPN](media/f5-sso-vpn/vpn-launcher.png)

Po výběru dlaždice VPN se nainstaluje hraniční klient pro velkou IP adresu a naváže se připojení VPN nakonfigurované pro SHA.
Aplikace F5 VPN by měla být v rámci podmíněného přístupu Azure AD viditelná také jako cílový prostředek. Projděte si naše [doprovodné](../conditional-access/concept-conditional-access-policies.md) materiály k sestavování zásad podmíněného přístupu a také povolení [ověřování uživatelů bez hesla](https://www.microsoft.com/security/business/identity/passwordless)Azure AD.