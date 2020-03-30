---
title: Vlastní domény v proxy aplikací azure ad | Dokumenty společnosti Microsoft
description: Konfigurace a správa vlastních domén v proxy aplikací azure ad.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f35658a75adb4d4c6c279e45087e741b8117e65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481377"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Konfigurace vlastních domén pomocí proxy aplikace Azure AD

Když publikujete aplikaci prostřednictvím proxy aplikace služby Azure Active Directory, vytvoříte pro uživatele externí adresu URL. Tato adresa URL získá výchozí *yourtenant.msappproxy.net*domény . Pokud například publikujete aplikaci s názvem *Výdaje* ve vašem tenantovi s názvem *Contoso*, externí adresa URL je *\/https: /expenses-contoso.msappproxy.net*. Pokud chcete místo *msappproxy.net*použít vlastní název domény , můžete pro aplikaci nakonfigurovat vlastní doménu. 

## <a name="benefits-of-custom-domains"></a>Výhody vlastních domén

Je vhodné nastavit vlastní domény pro vaše aplikace, kdykoli je to možné. Mezi důvody použití vlastních domén patří:

- Propojení mezi aplikacemi fungují i mimo podnikovou síť. Bez vlastní domény, pokud vaše aplikace má pevně zakódované interní odkazy na cíle mimo proxy aplikace a odkazy nejsou externě řešitelné, budou přerušit. Pokud jsou interní a externí adresy URL stejné, vyhnete se tomuto problému. Pokud nemůžete používat vlastní domény, najdete v článku [Přesměrování pevně zakódovaných odkazů pro aplikace publikované pomocí proxy aplikací Azure AD](../application-proxy-link-translation.md) pro jiné způsoby řešení tohoto problému. 
  
- Vaši uživatelé budou mít jednodušší prostředí, protože se mohou dostat do aplikace se stejnou adresou URL z evnitř nebo vně sítě. Nemusí se učit různé interní a externí adresy URL ani sledovat jejich aktuální polohu. 

- Můžete ovládat svou značku a vytvořit požadované adresy URL. Vlastní doména může pomoci vytvořit důvěru uživatelů, protože uživatelé místo *msappproxy.net*zobrazují a používají známé jméno .

- Některé konfigurace budou fungovat pouze s vlastními doménami. Například potřebujete vlastní domény pro aplikace, které používají kód Složky zabezpečení Markup Language (SAML), například když používáte službu AD FS (Active Directory Federation Services), ale nemůžete používat službu WS-Federation. Další informace naleznete [v tématu Práce s aplikacemi podporujícími deklarace identity v proxy aplikaci](application-proxy-configure-for-claims-aware-applications.md). 

Pokud se interní a externí adresy URL neshodují, není tak důležité používat vlastní domény, ale můžete využít další výhody. 

## <a name="dns-configuration-options"></a>Možnosti konfigurace DNS

V závislosti na požadavcích můžete nastavit konfiguraci DNS několika možnostmi:

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>Stejná interní a externí adresa URL, odlišné interní a externí chování 

Pokud nechcete, aby interní uživatelé byli směrováni prostřednictvím proxy aplikace, můžete nastavit *dns s rozděleným mozkem*. Rozdělená infrastruktura DNS směruje interní hostitele na interní server názvů domén a externí hostitele na externí server názvů domén pro překlad názvů. 

![„Schizofrenní“ DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>Různé interní a externí adresy URL 

Pokud se interní a externí adresy URL liší, není nutné konfigurovat chování rozdělených mozků, protože směrování uživatelů je určeno adresou URL. V takovém případě změníte pouze externí DNS a směrovat externí adresu URL do koncového bodu proxy aplikace. 

Když vyberete vlastní doménu pro externí adresu URL, na informačním panelu se zobrazí položka CNAME, kterou je třeba přidat k externímu poskytovateli DNS. Tyto informace můžete vždy zobrazit tak, že přejdete na stránku **proxy aplikace** aplikace.

## <a name="set-up-and-use-custom-domains"></a>Nastavení a používání vlastních domén

Chcete-li nakonfigurovat místní aplikaci pro použití vlastní domény, potřebujete ověřenou vlastní doménu Služby Azure Active Directory, certifikát PFX pro vlastní doménu a místní aplikaci ke konfiguraci. 

### <a name="create-and-verify-a-custom-domain"></a>Vytvoření a ověření vlastní domény

Vytvoření a ověření vlastní domény:

1. Ve službě Azure Active Directory vyberte v levé navigaci **vlastní názvy domén** a pak vyberte **Přidat vlastní doménu**. 
1. Zadejte svůj vlastní název domény a vyberte **Přidat doménu**. 
1. Na stránce domény zkopírujte informace o záznamu TXT pro vaši doménu. 
1. Přejděte na svého doménového registrátora a vytvořte nový txt záznam pro vaši doménu na základě zkopírovaných informací DNS.
1. Po registraci domény vyberte na stránce domény ve službě Azure Active Directory **možnost Ověřit**. Po **ověření**stavu domény můžete doménu použít ve všech konfiguracích Azure AD, včetně proxy aplikace. 

Podrobnější pokyny najdete v tématu [Přidání vlastního názvu domény pomocí portálu Azure Active Directory](../fundamentals/add-custom-domain.md).

### <a name="configure-an-app-to-use-a-custom-domain"></a>Konfigurace aplikace pro použití vlastní domény

Publikování aplikace prostřednictvím proxy aplikace s vlastní doménou:

1. U nové aplikace vyberte ve službě Azure Active Directory v levém navigačním panelu **podnikové aplikace.** Vyberte **Nová aplikace**. V části **Místní aplikace** vyberte Přidat **místní aplikaci**. 
   
   U aplikace, která už je v **podnikových aplikacích**, ji vyberte ze seznamu a v levém navigačním panelu vyberte **Proxy aplikace.** 

2. Na stránce Nastavení proxy aplikace zadejte **název,** pokud přidáváte vlastní místní aplikaci.

3.  Do pole **Interní adresa URL** zadejte interní adresu URL aplikace.
   
4. V poli **Externí adresa URL** rozbalte seznam a vyberte vlastní doménu, kterou chcete použít.
   
5. Vyberte **Přidat**.
   
   ![Vybrat vlastní doménu](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
6. Pokud doména již certifikát má, zobrazí se v poli **Certifikát** informace o certifikátu. V opačném případě vyberte pole **Certifikát.** 
   
   ![Kliknutím nahrajete certifikát.](./media/application-proxy-configure-custom-domain/certificate.png)
   
7. Na stránce **s certifikátem SSL** vyhledejte a vyberte soubor certifikátu PFX. Zadejte heslo certifikátu a vyberte **možnost Odeslat certifikát**. Další informace o certifikátech naleznete v části [Certifikáty pro vlastní domény.](#certificates-for-custom-domains)
   
   ![Nahrát certifikát](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > Vlastní doména potřebuje svůj certifikát nahrát pouze jednou. Poté se nahraný certifikát použije automaticky při použití vlastní domény pro jiné aplikace.
   
8. Pokud jste přidali certifikát, na stránce **Proxy aplikace** vyberte **Uložit**. 
   
9. Na informačním panelu na stránce **Proxy aplikace** si poznamenejte položku CNAME, kterou potřebujete přidat do zóny DNS. 
   
   ![Přidat položku CNAME DNS](./media/application-proxy-configure-custom-domain/dns-info.png)
   
10. Postupujte podle pokynů na [webu Správa záznamů DNS a sad záznamů pomocí portálu Azure pro](../../dns/dns-operations-recordsets-portal.md) přidání záznamu DNS, který přesměruje novou externí adresu URL do domény *msappproxy.net.*
   
11. Chcete-li zkontrolovat, zda je záznam DNS správně nakonfigurován, ověřte pomocí příkazu [nslookup,](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) že je externí adresa URL dostupná a *že se msapproxy.net* doména zobrazí jako alias.

Aplikace je nyní nastavena na použití vlastní domény. Nezapomeňte přiřadit uživatele k aplikaci před testováním nebo vydáním. 

Pokud chcete změnit doménu aplikace, vyberte jinou doménu z rozevíracího seznamu v **externí adrese URL** na stránce proxy **aplikace.** V případě potřeby nahrajte certifikát pro aktualizovanou doménu a aktualizujte záznam DNS. Pokud v rozevíracím seznamu v **externí adrese URL**nevidíte požadovanou vlastní doménu , nemusí být ověřena.

Podrobnější pokyny pro proxy aplikace [najdete v tématu Kurz: Přidání místní aplikace pro vzdálený přístup prostřednictvím proxy aplikace ve službě Azure Active Directory](application-proxy-add-on-premises-application.md).

## <a name="certificates-for-custom-domains"></a>Certifikáty pro vlastní domény

Certifikát vytvoří zabezpečené připojení TLS pro vaši vlastní doménu. 

### <a name="certificate-formats"></a>Formáty certifikátů

Musíte použít certifikát PFX, abyste zajistili, že jsou zahrnuty všechny požadované zprostředkující certifikáty. Certifikát musí obsahovat soukromý klíč.

Neexistuje žádné omezení na metody podpisu certifikátu. Je podporována kryptografie eliptické křivky (ECC), alternativní název předmětu (SAN) a další běžné typy certifikátů. 

Certifikáty se zástupnými symboly můžete použít, pokud se zástupný znak shoduje s externí adresou URL. Pro [aplikace se zástupnými symboly](application-proxy-wildcard.md)je nutné použít certifikáty se zástupnými symboly . Pokud chcete certifikát použít také pro přístup k subdoménám, je nutné přidat zástupné znaky subdomény jako alternativní názvy předmětů do stejného certifikátu. Například certifikát pro * \*.adventure-works.com* nebude fungovat pro * \*.apps.adventure-works.com,* pokud nepřidáte * \*.apps.adventure-works.com* jako alternativní název subjektu. 

Certifikáty vydané vlastní infrastrukturou veřejných klíčů (PKI) můžete použít, pokud je řetěz certifikátů nainstalován na vašich klientských zařízeních. Intune může tyto certifikáty nasadit do spravovaných zařízení. U nespravovaných zařízení je nutné tyto certifikáty nainstalovat ručně. 

Nedoporučujeme používat soukromou kořenovou certifikační autoritu, protože soukromá kořenová certifikační autorita by také musela být zatlačena do klientských počítačů, což může představovat mnoho výzev.

### <a name="certificate-management"></a>Správa certifikátů

Veškerá správa certifikátů probíhá prostřednictvím jednotlivých stránek aplikace. Přejděte na stránku **proxy aplikace aplikace** pro přístup k poli **Certifikát.**

Stejný certifikát můžete použít pro více aplikací. Pokud nahraný certifikát pracuje s jinou aplikací, bude použit automaticky. Při přidání nebo konfiguraci aplikace nebudete vyzváni k jeho znovunahrání. 

Po vypršení platnosti certifikátu se zobrazí upozornění, že máte nahrát jiný certifikát. Pokud je certifikát odvolán, může se uživatelům při přístupu k aplikaci zobrazit upozornění zabezpečení. Chcete-li aktualizovat certifikát pro aplikaci, přejděte na stránku **Proxy aplikace,** vyberte **Certifikát**a nahrajte nový certifikát. Pokud starý certifikát nepoužívá jiné aplikace, automaticky se odstraní. 

## <a name="next-steps"></a>Další kroky
* [Povolte jednotné přihlašování](application-proxy-configure-single-sign-on-with-kcd.md) k publikovaným aplikacím pomocí ověřování Azure AD.
* [Povolte podmíněný přístup](../conditional-access/overview.md) k publikovaným aplikacím.

