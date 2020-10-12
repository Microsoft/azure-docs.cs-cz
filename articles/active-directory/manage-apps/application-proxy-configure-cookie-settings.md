---
title: Nastavení souborů cookie proxy aplikace – Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) mají přístup a soubory cookie relace pro přístup k místním aplikacím prostřednictvím proxy aplikací. V tomto článku zjistíte, jak používat a konfigurovat nastavení souborů cookie.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 656841fc8e62e81318ffd568069c0664192b1747
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84764889"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Nastavení souborů cookie pro přístup k místním aplikacím v Azure Active Directory

Azure Active Directory (Azure AD) mají přístup a soubory cookie relace pro přístup k místním aplikacím prostřednictvím proxy aplikací. Zjistěte, jak používat nastavení souborů cookie proxy aplikace. 

## <a name="what-are-the-cookie-settings"></a>Co jsou nastavení souborů cookie?

[Proxy aplikace](application-proxy.md) používá následující nastavení přístupu a souborů cookie relace.

| Nastavení souborů cookie | Výchozí | Description | Doporučení |
| -------------- | ------- | ----------- | --------------- |
| Použít soubor cookie HTTP-Only | **Ne** | Hodnota **Ano** povolí proxy aplikace, aby do hlaviček http Response zahrnovala příznak HttpOnly. Tento příznak poskytuje další výhody zabezpečení, například brání skriptování na straně klienta (CSS) z kopírování a úprav souborů cookie.<br></br><br></br>Předtím, než jsme podporovali nastavení HTTP-Only, proxy aplikací zašifrované a přenesené soubory cookie přes zabezpečený kanál TLS, aby se chránily proti úpravám. | Použijte **Ano** z důvodu dalších výhod zabezpečení.<br></br><br></br>Pro klienty nebo uživatelské agenty, kteří potřebují přístup k souboru cookie relace, nepoužívejte **žádné** . Například pro klienta RDP nebo MTSC použijte **No** , který se připojuje k serveru Brána vzdálené plochy prostřednictvím proxy aplikací.|
| Použít zabezpečený soubor cookie | **Ne** | Hodnota **Ano** povolí proxy aplikace zahrnout zabezpečený příznak v hlavičkách http Response. Soubory cookie zabezpečení zvyšují zabezpečení tím, že přenáší soubory cookie přes zabezpečený kanál TLS, jako je například HTTPS. To brání tomu, aby soubory cookie byly pozorovány neoprávněnými stranami z důvodu přenosu souboru cookie ve formě prostého textu. | Použijte **Ano** z důvodu dalších výhod zabezpečení.|
| Použít trvalý soubor cookie | **Ne** | Hodnota **Ano** povolí proxy aplikace, aby při zavření webového prohlížeče nastavily soubory cookie pro přístup do vypršení platnosti. Trvalost trvá, dokud nevyprší platnost přístupového tokenu, nebo dokud uživatel neodstraní trvalé soubory cookie ručně. | Nepoužívejte **žádnou** z důvodu bezpečnostního rizika spojeného s udržením ověřených uživatelů.<br></br><br></br>Pro starší aplikace, které nemůžou sdílet soubory cookie mezi procesy, doporučujeme použít jenom **Ano** . Je lepší aktualizovat aplikaci tak, aby zpracovávala soubory cookie sdílení mezi procesy namísto použití trvalých souborů cookie. Můžete například potřebovat trvalé soubory cookie, které uživateli umožňují otevírat dokumenty Office v zobrazení Průzkumník z webu služby SharePoint. Bez trvalých souborů cookie Tato operace může selhat, pokud soubory cookie přístupu nejsou sdíleny mezi prohlížečem, procesem Průzkumníka a procesem Office. |

## <a name="samesite-cookies"></a>Soubory cookie SameSite
Od verze Chrome 80 a nakonec v prohlížečích využívajících chrom, soubory cookie, které nespecifikují atribut [SameSite](https://web.dev/samesite-cookies-explained) , budou považovány za, jako kdyby byly nastaveny na **SameSite = LAX**. Atribut SameSite deklaruje, jak by měly být soubory cookie omezeny na kontext stejného serveru. Když se nastaví na LAX, soubor cookie se pošle jenom do požadavků na stejnou lokalitu nebo do navigace na nejvyšší úrovni. Proxy aplikace ale vyžaduje, aby se tyto soubory cookie zachovaly v kontextu třetí strany, aby se uživatelé mohli při jejich relaci správně přihlásit. Z tohoto důvodu provádíme aktualizace přístupu k proxy aplikací a souborů cookie relace, aby se zabránilo nepříznivému dopadu této změny. Mezi tyto aktualizace patří:

* Nastavení atributu **SameSite** na **hodnotu None**. To umožňuje, aby se soubory cookie přístupu a relací proxy aplikací správně odesílaly v kontextu třetí strany.
* Nastavení **použít zabezpečený soubor cookie** pro použití výchozí hodnoty **Ano** . Chrom také vyžaduje, aby soubory cookie určovaly zabezpečený příznak, nebo bude odmítnut. Tato změna se bude vztahovat na všechny existující aplikace publikované prostřednictvím proxy aplikací. Všimněte si, že soubory cookie pro přístup k proxy aplikacím jsou vždycky nastavené na zabezpečení a přenášejí se přes HTTPS. Tato změna se bude vztahovat pouze na soubory cookie relace.

Tyto změny souborů cookie aplikace proxy se budou zavádět v průběhu příštích několika týdnů před datem vydání verze Chrome 80.

Navíc platí, že pokud vaše back-end aplikace obsahuje soubory cookie, které potřebují být k dispozici v kontextu třetí strany, musíte explicitně vyjádřit výslovný souhlas změnou aplikace tak, aby pro tyto soubory cookie používala SameSite = None. Proxy aplikace překládá hlavičku Set-Cookie na její adresy URL a bude respektovat nastavení pro tyto soubory cookie nastavené back-end aplikací.



## <a name="set-the-cookie-settings---azure-portal"></a>Nastavení souborů cookie – Azure Portal
Nastavení souborů cookie pomocí Azure Portal:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com). 
2. Přejděte na **Azure Active Directory**   >  **podnikové aplikace**   >  **všechny aplikace**.
3. Vyberte aplikaci, pro kterou chcete povolit nastavení souborů cookie.
4. Klikněte na **proxy aplikace**.
5. V části **Další nastavení**nastavte nastavení souborů cookie na **Ano** nebo **ne**.
6. Kliknutím na **Uložit** použijte změny. 

## <a name="view-current-cookie-settings---powershell"></a>Zobrazit aktuální nastavení souborů cookie – PowerShell

Pokud chcete zobrazit aktuální nastavení souborů cookie pro aplikaci, použijte tento příkaz PowerShellu:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Nastavení souborů cookie – PowerShell

V následujících příkazech PowerShell ```<ObjectId>``` je identifikátor objektu aplikace. 

**Soubor cookie pouze s protokolem HTTP** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Zabezpečený soubor cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Trvalé soubory cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
