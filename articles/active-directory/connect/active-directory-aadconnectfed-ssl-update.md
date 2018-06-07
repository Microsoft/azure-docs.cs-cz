---
title: Azure AD Connect – aktualizujte certifikát SSL pro farmu služby AD FS | Microsoft Docs
description: Tato dokument podrobně popisuje postup aktualizace certifikátu SSL farmu služby AD FS pomocí Azure AD Connect.
services: active-directory
keywords: služby Azure ad connect, aktualizace ssl služby AD FS, aktualizace certifikátů služby AD FS, změnit certifikát služby AD FS, nový certifikát služby AD FS, certifikát služby AD FS, aktualizace služby AD FS certifikát ssl, aktualizace ssl certifikát služby AD FS, nakonfigurovat certifikát ssl služby AD FS, služba AD FS, ssl, certifikát, certifikát komunikace služby AD FS, federation aktualizace, konfigurace federace, aad connect
authors: anandyadavmsft
manager: mtillman
editor: billmath
ms.assetid: 7c781f61-848a-48ad-9863-eb29da78f53c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.component: hybrid
ms.author: anandy
ms.custom: seohack1
ms.openlocfilehash: d657725b7bfb75a118c1d8bbbe701c1c121b1ef2
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34594998"
---
# <a name="update-the-ssl-certificate-for-an-active-directory-federation-services-ad-fs-farm"></a>Aktualizovat certifikát SSL pro farmy služby Active Directory Federation Services (AD FS)

## <a name="overview"></a>Přehled
Tento článek popisuje, jak používat Azure AD Connect aktualizovat certifikát SSL pro farmy služby Active Directory Federation Services (AD FS). Nástroj Azure AD Connect můžete snadno aktualizovat certifikát SSL pro farmy služby AD FS, i když uživatel přihlásit metoda vybrané není služby AD FS.

Můžete provést celou operaci aktualizace certifikát SSL pro farmy služby AD FS ve všech federace a servery Proxy webové aplikace (WAP) ve třech jednoduchých kroků:

![Tři kroky](./media/active-directory-aadconnectfed-ssl-update/threesteps.png)


>[!NOTE]
>Další informace o certifikátech, které používají služby AD FS najdete v tématu [Principy certifikátů používaných službou AD FS](https://technet.microsoft.com/library/cc730660.aspx).

## <a name="prerequisites"></a>Požadavky

* **Farma služby AD FS**: Ujistěte se, že farmu služby AD FS je založená na Windows Server 2012 R2 nebo novější.
* **Azure AD Connect**: Zkontrolujte, zda je verze služby Azure AD Connect 1.1.553.0 nebo vyšší. Použijete-li úlohu **certifikát SSL služby FS aktualizace AD**.

![Úloha aktualizace SSL](./media/active-directory-aadconnectfed-ssl-update/updatessltask.png)

## <a name="step-1-provide-ad-fs-farm-information"></a>Krok 1: Zadání informací farmy služby AD FS

Azure AD Connect se pokusí získat informace o farmu služby AD FS automaticky:
1. Dotaz na informace farmy služby AD FS (Windows Server 2016 nebo novější).
2. Odkazy na informace z předchozích spuštění, které jsou uložené místně službou Azure AD Connect.

Můžete upravit seznam serverů, které se zobrazují přidáním nebo odebráním servery tak, aby odrážela aktuální konfiguraci farmy služby AD FS. Jakmile se poskytuje informace o serveru, Azure AD Connect zobrazí připojení a aktuální stav certifikátu protokolu SSL.

![Informace o serveru AD FS](./media/active-directory-aadconnectfed-ssl-update/adfsserverinfo.png)

Pokud seznam obsahuje server, který je už součástí farmy služby AD FS, klikněte na tlačítko **odebrat** se odstranit server ze seznamu serverů ve farmě služby AD FS.

![Offline serveru v seznamu](./media/active-directory-aadconnectfed-ssl-update/offlineserverlist.png)

>[!NOTE]
> Odebrání serveru ze seznamu serverů pro farmu služby AD FS ve službě Azure AD Connect je místní operace a aktualizuje informace pro farmu služby AD FS, která udržuje Azure AD Connect místně. Azure AD Connect nezmění konfigurace ve službě AD FS, aby odrážely změny.    

## <a name="step-2-provide-a-new-ssl-certificate"></a>Krok 2: Zadejte nový certifikát SSL

Když jste potvrdili informace o servery ve farmě služby AD FS, Azure AD Connect požádá o nový certifikát SSL. Zadejte certifikát PFX chráněný heslem pokračujte v instalaci.

![Certifikát SSL](./media/active-directory-aadconnectfed-ssl-update/certificate.png)

Po zadání certifikát Azure AD Connect projde řadu požadavky. Ověření certifikátu k zajištění, že certifikát je správný pro farmu služby AD FS:

-   Předmět názvu nebo alternativní název subjektu certifikátu je buď stejný jako název federační služby, nebo je certifikát se zástupným znakem.
-   Certifikát je platný pro více než 30 dní.
-   Řetězu certifikátů je platný.
-   Certifikát je chráněný heslem.

## <a name="step-3-select-servers-for-the-update"></a>Krok 3: Vyberte servery pro aktualizaci

V dalším kroku vyberte servery, které je potřeba aktualizovat certifikát SSL. Servery, které jsou offline nelze vybrat pro aktualizaci.

![Vyberte servery a aktualizace](./media/active-directory-aadconnectfed-ssl-update/selectservers.png)

Po dokončení konfigurace Azure AD Connect zobrazí zprávu, která označuje stav aktualizace a nabízí možnost ověření přihlášení služby AD FS.

![Dokončení konfigurace](./media/active-directory-aadconnectfed-ssl-update/configurecomplete.png)   

## <a name="faqs"></a>Nejčastější dotazy

* **Název subjektu certifikátu pro nový certifikát SSL služby AD FS, co by měla být?**

    Azure AD Connect kontroluje, zda subjektu název nebo alternativní název subjektu certifikátu obsahuje název federační služby. Například pokud je název vaší služby federačního serveru fs.contoso.com, předmět názvu nebo alternativní název subjektu musí být fs.contoso.com.  Certifikáty se zástupnými znaky, jsou také přijaty.

* **Proč se zobrazuje zpráva pro přihlašovací údaje znovu na stránce server WAP?**

    Pokud přihlašovacích údajů, které zadáte pro připojení k serverům služby AD FS taky nemáte oprávnění ke správě serverů WAP, pak Azure AD Connect vyzve k zadání přihlašovacích údajů, které mají oprávnění správce na serverech WAP.

* **Serveru se zobrazuje v režimu offline. Co bych měl/a dělat?**

    Azure AD Connect nelze provádět všechny operace, pokud je server offline. Pokud je server součástí farmy služby AD FS, zkontrolujte připojení k serveru. Poté, co jste vyřešit problém, stiskněte ikonu aktualizace se bude aktualizovat stav v průvodci. Pokud byl server součástí farmy dříve, ale teď už existuje, klikněte na tlačítko **odebrat** odstranit ze seznamu serverů, že Azure AD Connect udržuje. Odebrání serveru ze seznamu ve službě Azure AD Connect není změnit konfiguraci služby AD FS, sám sebe. Pokud používáte služby AD FS v systému Windows Server 2016 nebo novější, zůstanou serveru v nastavení konfigurace a se zobrazí při příštím spuštění úlohy.

* **Můžete aktualizovat podmnožinu Moje servery ve farmě s novým certifikátem SSL?**

    Ano. Možné vždy spouštět úlohy **aktualizovat certifikát SSL** aktualizovat na ostatní servery. Na **vyberte servery pro protokol SSL certifikát aktualizace** stránky, lze seřadit seznam serverů na **datum vypršení platnosti SSL** snadný přístup k serveru, které ještě nejsou aktualizovány.

* **Po odebrání serveru při předchozím spuštění, ale stále se zobrazuje jako offline a uvedené na stránce servery služby AD FS. Proč je offline serveru stále existují i po jeho po odebrání?**

    Odebrání serveru ze seznamu ve službě Azure AD Connect, neodstraní se v konfiguraci služby AD FS. Azure AD Connect odkazuje služby AD FS (Windows Server 2016 nebo vyšší) pro žádné informace o farmy. Pokud je server stále existuje v konfiguraci služby AD FS, objeví se zpět v seznamu.  

## <a name="next-steps"></a>Další postup

- [Azure AD Connect a federace](active-directory-aadconnectfed-whatis.md)
- [Přizpůsobení službou Azure AD Connect a správy služby Active Directory Federation Services](active-directory-aadconnect-federation-management.md)
