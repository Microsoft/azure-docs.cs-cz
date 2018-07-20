---
title: Konfigurovat rozšíření Azure MFA NPS | Dokumentace Microsoftu
description: Po instalaci rozšíření NPS tyto kroky použijte pro pokročilé konfigurace, jako jsou přidávání na seznam povolených IP a nahrazení (UPN).
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: a857732bcbe70cec164cebb54d7c09a1f103a942
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160606"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Rozšířené možnosti konfigurace pro rozšíření NPS pro ověřování službou Multi-Factor Authentication

Server NPS (Network Policy Server) rozšíření – rozšiřující funkce založené na cloudu Azure Multi-Factor Authentication do místní infrastruktury. Tento článek předpokládá, že již máte nainstalované rozšíření a nyní chcete vědět, jak přizpůsobit rozšíření pro vás potřebám. 

## <a name="alternate-login-id"></a>Alternativním přihlašovacím ID

Vzhledem k tomu, že rozšíření NPS se připojuje k místním i cloudovým adresáře, může dojít k problému, kdy hlavní názvy vaše místní uživatele (UPN) neodpovídají názvům v cloudu. Pokud chcete tento problém vyřešit, použijte alternativní přihlašovací ID. 

V rámci rozšíření serveru NPS můžete určit atribut služby Active Directory, který má být zastoupen hlavní název uživatele pro ověřování Azure Multi-Factor Authentication. To umožňuje chránit prostředky místní s dvoustupňovým ověřováním beze změny vaší místní UPN. 

Konfigurace alternativního přihlašovacího ID, přejděte na `HKLM\SOFTWARE\Microsoft\AzureMfa` a upravit následující hodnoty registru:

| Název | Typ | Výchozí hodnota | Popis |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | řetězec | Prázdné | Určete název atributu služby Active Directory, kterou chcete použít místo hlavní název uživatele. Tento atribut se používá jako atribut AlternateLoginId. Pokud tato hodnota registru je nastavena [platný atribut služby Active Directory](https://msdn.microsoft.com/library/ms675090.aspx) (pro příklad, e-mailu nebo displayName), pak hodnota atributu je zastoupen uživatele (UPN) pro ověřování. Pokud tato hodnota registru je prázdný nebo není nakonfigurovaná, pak AlternateLoginId zakázána a uživatele (UPN) se používá k ověřování. |
| LDAP_FORCE_GLOBAL_CATALOG | Boolean | False | Pomocí tohoto příznaku při hledání AlternateLoginId vynutí použití globálního katalogu pro hledání LDAP. Konfigurace řadiče domény jako globální katalog, přidejte atribut AlternateLoginId ke globálnímu katalogu a pak povolte tento příznak. <br><br> Pokud je nakonfigurovaný (Neprázdné), LDAP_LOOKUP_FORESTS **tento příznak se vynucuje jako true**bez ohledu hodnotu daného nastavení registru. V takovém případě rozšíření NPS vyžaduje globální katalog nakonfigurovat s atributem AlternateLoginId pro každou doménovou strukturu. |
| LDAP_LOOKUP_FORESTS | řetězec | Prázdné | Zadejte středníkem oddělený seznam doménových struktur pro hledání. Například *contoso.com;foobar.com*. Pokud je tato hodnota registru nakonfigurovaný, rozšíření NPS zavádět postupně vyhledá všechny doménové struktury v pořadí, ve kterém byly uvedeny a vrátí první hodnotu AlternateLoginId úspěšné. Pokud tato hodnota registru neuděláte, AlternateLoginId vyhledávání je omezena na aktuální doménu.|

Řešení potíží s alternativním přihlašovacím ID, použít doporučené kroky pro [alternativní přihlašovací ID chyby](howto-mfa-nps-extension-errors.md#alternate-login-id-errors).

## <a name="ip-exceptions"></a>Výjimky protokolu IP

Pokud je potřeba sledovat dostupnost serveru, jako je-li ověřit nástroje pro vyrovnávání zatížení serverů, které jsou spuštěny před odesláním úloh, nechcete, aby tyto kontroly k žádosti o ověření blokovat. Místo toho vytvořte seznam IP adres, které používají účty služeb a požadavky na Vícefaktorové ověřování pro tento seznam zakázat. 

Pokud chcete nakonfigurovat seznam povolených IP adres, přejděte na `HKLM\SOFTWARE\Microsoft\AzureMfa` a zadejte následující hodnotu registru: 

| Název | Typ | Výchozí hodnota | Popis |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | řetězec | Prázdné | Zadejte středníkem oddělený seznam IP adres. Zahrnout IP adresy počítačů, kde žádosti o služby mají původ, třeba server NAS/VPN. Rozsahy IP adres jsou podsítě se nepodporují. <br><br> Například *10.0.0.1;10.0.0.2;10.0.0.3*.

Když žádost pocházejí z IP adresu, která existuje v seznamu povolených, se přeskočí dvoustupňové ověřování. Seznam povolených IP adres je ve srovnání s IP adresu, která je součástí *ratNASIPAddress* atribut směrovat požadavky protokolu RADIUS. Pokud požadavku protokolu RADIUS je k dispozici ve bez atributu ratNASIPAddress, je zaznamenána následující upozornění: "Seznamu povolených IP adres P_WHITE_LIST_WARNING::IP ignoruje jako zdrojová IP adresa v požadavku protokolu RADIUS v atributu NasIpAddress nebylo nalezeno."

## <a name="next-steps"></a>Další postup

[řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)