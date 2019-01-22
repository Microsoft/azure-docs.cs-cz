---
title: Ověřování služby IIS a server Azure MFA | Dokumentace Microsoftu
description: Nasazení ověření služby IIS a serveru Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: b3cf329562b10ea73f02a5bfe933a1caf8fe4607
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54434321"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Konfigurace serveru Azure Multi-Factor Authentication pro webové aplikace IIS

Část Ověření služby IIS Azure Multi-Factor Authentication (MFA) Serveru použijte k povolení a konfiguraci ověřování služby IIS pro integraci s webovými aplikacemi IIS Microsoftu. Azure MFA Server nainstaluje modul plug-in, který dokáže filtrovat požadavky prováděné na webovém serveru IIS, aby bylo možné přidat Azure Multi-Factor Authentication. Modul plug-in služby IIS poskytuje podporu pro ověřování na základě formuláře a integrované HTTP ověřování systému Windows. Důvěryhodné IP adresy mohou být také nakonfigurovány k vyloučení interních IP adres z dvoufaktorového ověřování.

![Ověřování IIS](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Ověřování založené na formulářích služby IIS pomocí serveru Azure Multi-Factor Authentication
Pro zabezpečení webové aplikace služby IIS, která používá ověřování založené na formulářích, nainstalujte Azure Multi-Factor Authentication Server na webový server služby IIS a nakonfigurujte server podle následujícího postupu:

1. V Azure Multi-Factor Authentication Serveru klikněte na ikonu Ověřování IIS v levé nabídce.
2. Klikněte na kartu **Založené na formulářích**.
3. Klikněte na tlačítko **Add** (Přidat).
4. Pokud chcete automaticky detekovat uživatelské jméno, heslo a proměnné domény, zadejte adresu URL pro přihlášení (například https://localhost/contoso/auth/login.aspx) v dialogovém okně Automatická konfigurace webové stránky s formuláři a klikněte na **OK**.
5. Zaškrtněte políčko **Vyžadovat porovnání uživatele u služby Multi-Factor Authentication**, pokud se všichni uživatelé importovali nebo budou importovat na server a budou podléhat vícefaktorovému ověřování. Pokud dosud nebyl importován velký počet uživatelů do serveru a/nebo budou vyloučeni z Multi-Factor Authentication, nechejte pole nezaškrtnuté.
6. Pokud proměnné stránky nejde rozpoznat automaticky, v dialogovém okně Automatická konfigurace webové stránky s formuláři klikněte na **Zadat ručně**.
7. V dialogovém okně Přidat web na základě formuláře zadejte adresu URL do přihlašovací stránky v poli Adresa URL pro odeslání a zadejte název aplikace (volitelný). Název aplikace se zobrazí v sestavách Azure Multi-Factor Authentication a může se zobrazit v rámci SMS zpráv nebo mobilních aplikací ověřování.
8. Vyberte správný formát požadavku. Tato hodnota je u většiny webových aplikací nastavena na **POST nebo GET**.
9. Zadejte proměnné pro uživatelské jméno, heslo a doménu (pokud se zobrazí na stránce přihlášení). Pokud chcete vyhledat názvy vstupních polí, přejděte ve webovém prohlížeči na přihlašovací stránku, klikněte na ni pravým tlačítkem a vyberte **Zobrazit zdrojový kód**.
10. Zaškrtněte políčko **Vyžadovat porovnání uživatele u služby Multi-Factor Authentication**, pokud se všichni uživatelé importovali nebo budou importovat na server a budou podléhat vícefaktorovému ověřování. Pokud dosud nebyl importován velký počet uživatelů do serveru a/nebo budou vyloučeni z Multi-Factor Authentication, nechejte pole nezaškrtnuté.
11. Po kliknutí na **Upřesnit** můžete zkontrolovat upřesňující nastavení:

  - Výběr vlastního souboru odmítnutí stránky
  - Ukládání úspěšných ověření na webu na určitou dobu do mezipaměti s využitím souborů cookie
  - Vyberte, jestli chcete primární přihlašovací údaje ověřit pomocí domény Windows, adresáře LDAP nebo serveru RADIUS.

12. Kliknutím na **OK** se vraťte do dialogového okna Přidat webovou stránku s formuláři.
13. Klikněte na **OK**.
14. Po zjištění nebo zadání adresy URL a proměnných hodnot stránek se data webové stránky zobrazí v panelu založeném na formulářích.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Používání integrovaného ověřování systému Windows pomocí serveru Azure Multi-Factor Authentication Server
Pro zabezpečení webové aplikace služby IIS, která používá integrované HTTP ověřování systému Windows, nainstalujte Azure MFA Server na webový server služby IIS a nakonfigurujte ho pomocí následujících kroků:

1. V Azure Multi-Factor Authentication Serveru klikněte na ikonu Ověřování IIS v levé nabídce.
2. Klikněte na kartu **HTTP**.
3. Klikněte na tlačítko **Add** (Přidat).
4. V dialogovém okně Přidat základní adresu URL zadejte adresu URL pro webovou stránku, kde se provádí ověřování HTTP (například http://localhost/owa)), a zadejte název aplikace (volitelné). Název aplikace se zobrazí v sestavách Azure Multi-Factor Authentication a může se zobrazit v rámci SMS zpráv nebo mobilních aplikací ověřování.
5. Pokud je výchozí hodnota nedostatečná, upravte časový limit nečinnosti a maximální dobu trvání relace.
6. Zaškrtněte políčko **Vyžadovat porovnání uživatele u služby Multi-Factor Authentication**, pokud se všichni uživatelé importovali nebo budou importovat na server a budou podléhat vícefaktorovému ověřování. Pokud dosud nebyl importován velký počet uživatelů do serveru a/nebo budou vyloučeni z Multi-Factor Authentication, nechejte pole nezaškrtnuté.
7. V případě potřeby zaškrtněte políčko pro **mezipaměť souborů cookie**.
8. Klikněte na **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Povolit moduly plug-in služby IIS a server Azure Multi-Factor Authentication
Po konfiguraci nastavení a adres URL založených na formulářích nebo adres URL ověřování HTTP musíte vybrat umístění, kde by se měly moduly plug-in Azure Multi-Factor Authentication IIS nahrát a povolit ve službě IIS. Použijte následující postup:

1. Pokud používáte IIS 6, klikněte na kartu **ISAPI**. Vyberte web, pod kterým se webová aplikace spouští (třeba výchozí web), a pro tento web povolte modul plug-in filtru ISAPI Azure Multi-Factor Authentication.
2. Pokud používáte IIS 7 nebo vyšší, klikněte na kartu **Nativní modul**. Vyberte servery, weby nebo aplikace a povolte modul plug-in IIS na požadovaných úrovních.
3. Zaškrtněte políčko **Povolit ověřování IIS** v horní části obrazovky. Azure Multi-Factor Authentication nyní zabezpečuje vybrané aplikace služby IIS. Ověřte, zda byli uživatelé naimportováni do serveru.

## <a name="trusted-ips"></a>Důvěryhodné IP adresy
Důvěryhodné IP adresy umožňují uživatelům obejít ověřování Azure Multi-Factor Authentication u požadavků webů pocházejících z konkrétní IP adresy nebo podsítě. Například můžete chtít vyloučit uživatele z ověřování Azure Multi-Factor Authentication při přihlašování z kanceláře. V takovém případě zadáte podsíť kanceláře jako položku důvěryhodných IP adres. Ke konfiguraci důvěryhodných IP adres použijte následující postup:

1. V části ověření služby IIS klikněte na kartu **Důvěryhodné IP adresy**.
2. Klikněte na tlačítko **Add** (Přidat).
3. Jakmile se zobrazí dialogové okno Přidat důvěryhodné IP adresy, vyberte přepínač **Samostatná IP adresa**, **Rozsah IP adres** nebo **Podsíť**.
4. Zadejte IP adresu, rozsah IP adres nebo podsíť, které chcete zařadit na seznam povolených adres. Pokud zadáváte podsíť, vyberte příslušnou síťovou masku a klikněte na **OK**. Seznam povolených adres byl přidán.
