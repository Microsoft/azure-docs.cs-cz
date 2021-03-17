---
title: Vlastní domény v Azure Proxy aplikací služby AD
description: Konfigurace a Správa vlastních domén v Azure Proxy aplikací služby AD.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/24/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 49edab5ed20749cb705d160e5cdc46a16bdee951
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258027"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Konfigurace vlastních domén s využitím proxy aplikací Azure AD

Když publikujete aplikaci prostřednictvím Proxy aplikací služby Azure Active Directory, vytvoříte externí adresu URL pro uživatele. Tato adresa URL získá výchozí *yourtenant.msappproxy.NET* domény. Například pokud publikujete aplikaci s názvem *výdaje* ve vašem tenantovi s názvem *Contoso*, externí adresa URL je *https: \/ /Expenses-contoso.msappproxy.NET*. Pokud chcete místo *msappproxy.NET* použít vlastní název domény, můžete pro svou aplikaci nakonfigurovat vlastní doménu. 

## <a name="benefits-of-custom-domains"></a>Výhody vlastních domén

Pokud je to možné, je vhodné nastavit vlastní domény pro aplikace. Mezi důvody pro použití vlastních domén patří:

- Propojení mezi aplikacemi funguje i mimo podnikovou síť. Pokud vaše aplikace obsahuje pevně zakódované interní odkazy na cíle mimo proxy server aplikace a odkazy se nedají externě přeložit, přestanou být bez vlastní domény. Pokud jsou vaše interní a externí adresy URL stejné, vyhnete se tomuto problému. Pokud nemůžete použít vlastní domény, přečtěte si téma [přesměrování pevně zakódované odkazů pro aplikace publikované pomocí Azure proxy aplikací služby AD](./application-proxy-configure-hard-coded-link-translation.md) , kde najdete další způsoby, jak tento problém vyřešit. 
  
- Uživatelé budou mít snazší možnosti, protože se můžou dostat do aplikace se stejnou adresou URL, která se nachází uvnitř nebo vně vaší sítě. Nepotřebují se učit různé interní a externí adresy URL nebo sledovat jejich aktuální umístění. 

- Můžete ovládat branding a vytvořit požadované adresy URL. Vlastní doména může pomáhat při sestavování důvěry vašich uživatelů, protože uživatelé uvidí a používají známý název místo *msappproxy.NET*.

- Některé konfigurace budou fungovat jenom s vlastními doménami. Například potřebujete vlastní domény pro aplikace, které používají Security Assertion Markup Language (SAML), například pokud používáte Active Directory Federation Services (AD FS) (AD FS), ale nemůžete použít WS-Federation. Další informace najdete v tématu [práce s aplikacemi pracujícími s deklaracemi v proxy aplikaci](application-proxy-configure-for-claims-aware-applications.md). 

Pokud nemůžete, aby se interní a externí adresy URL shodovaly, nemusíte používat vlastní domény, ale můžete využít i další výhody. 

## <a name="dns-configuration-options"></a>Možnosti konfigurace DNS

Konfigurace DNS v závislosti na vašich požadavcích je několik možností:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Stejná interní a externí adresa URL, jiné vnitřní a vnější chování 

Pokud nechcete, aby interní uživatelé procházeli prostřednictvím proxy aplikace, můžete nastavit *DNS pro dělené mozek*. Rozdělená infrastruktura DNS směruje interní hostitele na interní názvový server domény a externí hostitele na externí názvový server domény, aby bylo možné překlad názvů. 

![„Schizofrenní“ DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Různé interní a externí adresy URL 

Pokud se interní a externí adresy URL liší, nemusíte konfigurovat chování funkce Split-mozek, protože směrování uživatelů je určeno adresou URL. V takovém případě změníte pouze externí DNS a trasu externí adresu URL koncového bodu proxy aplikace. 

Když pro externí adresu URL vyberete vlastní doménu, zobrazí informační panel záznam CNAME, který potřebujete přidat k externímu poskytovateli DNS. Tyto informace můžete vždy zobrazit na stránce **proxy aplikací** aplikace.

## <a name="set-up-and-use-custom-domains"></a>Nastavení a použití vlastních domén

Pokud chcete nakonfigurovat místní aplikaci tak, aby používala vlastní doménu, budete potřebovat ověřenou Azure Active Directory vlastní doménu, certifikát PFX pro vlastní doménu a místní aplikaci, která se má nakonfigurovat. 

### <a name="create-and-verify-a-custom-domain"></a>Vytvoření a ověření vlastní domény

Vytvoření a ověření vlastní domény:

1. V Azure Active Directory v levém navigačním panelu vyberte **vlastní názvy domén** a pak vyberte **Přidat vlastní doménu**. 
1. Zadejte vlastní název domény a vyberte **Přidat doménu**. 
1. Na stránce doména zkopírujte informace o záznamu TXT pro vaši doménu. 
1. V závislosti na kopírovaných informacích DNS vyhledejte svůj doménový registrátor a vytvořte nový záznam TXT pro vaši doménu.
1. Po registraci domény na stránce domény v Azure Active Directory vyberte **ověřit**. Po **ověření** stavu domény můžete použít doménu napříč všemi vašimi konfiguracemi služby Azure AD, včetně proxy aplikací. 

Podrobnější pokyny najdete v tématu [Přidání vlastního názvu domény pomocí portálu Azure Active Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Konfigurace aplikace pro použití vlastní domény

Publikování aplikace prostřednictvím proxy aplikací s vlastní doménou:

1. V případě nové aplikace v Azure Active Directory v levém navigačním panelu vyberte **podnikové aplikace** . Vyberte **Nová aplikace**. V části místní **aplikace** vyberte **Přidat místní aplikaci**. 
   
   Pro aplikaci, která už je v **podnikových aplikacích**, vyberte ji ze seznamu a potom v levém navigačním panelu vyberte **proxy aplikace** . 

2. Na stránce nastavení proxy aplikace zadejte **název** , pokud přidáváte vlastní místní aplikaci.

3.  Do pole **interní adresa URL** zadejte interní adresu URL pro vaši aplikaci.
   
4. V poli **externí adresa URL** vyřaďte seznam a vyberte vlastní doménu, kterou chcete použít.
   
5. Vyberte **Přidat**.
   
   ![Vybrat vlastní doménu](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Pokud již doména obsahuje certifikát, zobrazí se v poli **certifikát** informace o certifikátu. V opačném případě vyberte pole **certifikát** .
   
   ![Kliknutím Nahrajte certifikát.](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Na stránce **certifikát SSL** vyhledejte a vyberte soubor certifikátu PFX. Zadejte heslo pro certifikát a vyberte **Odeslat certifikát**. Další informace o certifikátech najdete v části [certifikáty pro vlastní domény](#certificates-for-custom-domains) . Pokud certifikát není platný nebo došlo k potížím s heslem, zobrazí se chybová zpráva. [Nejčastější dotazy k proxy aplikacím](application-proxy-faq.md#application-configuration) obsahují některé kroky pro řešení potíží, které můžete vyzkoušet.
   
   ![Nahrát certifikát](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Vlastní doména vyžaduje, aby se certifikát nahrál jenom jednou. Nahraný certifikát se pak použije automaticky, když použijete vlastní doménu pro jiné aplikace.
   
8. Pokud jste přidali certifikát, na stránce **proxy aplikace** vyberte **Uložit**. 
   
9. Na informačním panelu na stránce **proxy aplikace** si poznamenejte záznam CNAME, který potřebujete přidat do zóny DNS. 
   
   ![Přidat záznam DNS CNAME](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Postupujte podle pokynů v tématu [Správa záznamů a sad záznamů DNS pomocí Azure Portal](../../dns/dns-operations-recordsets-portal.md) k přidání záznamu DNS, který přesměruje novou externí adresu URL do domény *msappproxy.NET* .
   
11. Pokud chcete zkontrolovat, jestli je záznam DNS správně nakonfigurovaný, pomocí příkazu [nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) potvrďte, že je vaše externí adresa URL dostupná, a doména *msapproxy.NET* se zobrazí jako alias.

Vaše aplikace je teď nastavená tak, aby používala vlastní doménu. Nezapomeňte přiřadit uživatele k aplikaci, než ji otestujete nebo vydáte. 

Pokud chcete změnit doménu pro aplikaci, vyberte v rozevíracím seznamu **externí adresy URL** na stránce **proxy aplikací** aplikace jinou doménu. V případě potřeby Nahrajte certifikát pro aktualizovanou doménu a aktualizujte záznam DNS. Pokud v rozevíracím seznamu **externí adresy URL** nevidíte požadovanou vlastní doménu, nemusí být ověřena.

Podrobnější pokyny pro proxy aplikací najdete v tématu [kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace v Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certifikáty pro vlastní domény

Certifikát vytvoří zabezpečené připojení TLS pro vaši vlastní doménu. 

### <a name="certificate-formats"></a>Formáty certifikátů

Abyste měli jistotu, že jsou zahrnuté všechny požadované zprostředkující certifikáty, musíte použít certifikát PFX. Certifikát musí obsahovat privátní klíč.

Jsou podporovány nejběžnější metody signatur certifikátů, například alternativní název subjektu (SAN). 

Můžete použít certifikáty se zástupnými znaky, pokud se zástupný znak shoduje s externí adresou URL. Pro [aplikace se zástupnými znaky](application-proxy-wildcard.md)musíte použít certifikáty se zástupnými znaky. Chcete-li použít certifikát k přístupu k subdoménám, je nutné přidat zástupné znaky subdomény jako alternativní názvy předmětu do stejného certifikátu. Například certifikát pro *\* . Adventure-Works.com* nebude fungovat pro *\* . Apps.Adventure-Works.com* , pokud nepřidáte *\* . Apps.Adventure-Works.com* jako alternativní název subjektu. 

Pokud je v klientských zařízeních nainstalovaný řetěz certifikátů, můžete použít certifikáty vydané vlastní infrastrukturou veřejných klíčů (PKI). Intune může tyto certifikáty nasadit na spravovaná zařízení. U nespravovaných zařízení musíte tyto certifikáty nainstalovat ručně. 

Nedoporučujeme používat privátní kořenovou certifikační autoritu, protože privátní kořenová certifikační autorita by taky musela být vložená do klientských počítačů, což může vést k mnoha problémům.

### <a name="certificate-management"></a>Správa certifikátů

Správa všech certifikátů probíhá prostřednictvím stránek jednotlivých aplikací. Pro přístup k poli **certifikátu** přejděte na stránku **proxy aplikací** aplikace.

Po nahrání certifikátu pro aplikaci se taky automaticky použije na **nové** nakonfigurované aplikace, které používají stejný certifikát. Certifikát budete muset znovu nahrát pro existující aplikace ve vašem tenantovi.

Po vypršení platnosti certifikátu se zobrazí upozornění, abyste si nahráli jiný certifikát. Pokud je certifikát odvolaný, můžou se uživatelům při přístupu k aplikaci zobrazit upozornění zabezpečení. Pokud chcete aktualizovat certifikát pro aplikaci, přejděte na stránku **proxy aplikace** pro aplikaci, vyberte **certifikát** a nahrajte nový certifikát. Pokud starý certifikát nepoužívá jiné aplikace, automaticky se odstraní. 

## <a name="next-steps"></a>Další kroky

* [Povolte jednotné přihlašování](application-proxy-configure-single-sign-on-with-kcd.md) k publikovaným aplikacím pomocí ověřování Azure AD.
* [Podmíněný přístup](../conditional-access/concept-conditional-access-cloud-apps.md) pro vaše publikované cloudové aplikace.