---
title: Konfigurace rozšíření Azure MFA NPS – Azure Active Directory
description: Po instalaci rozšíření NPS použijte tyto kroky pro pokročilou konfiguraci, jako je seznam povolených adres IP a nahrazení hlavního zápisu.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ea5b4f52fc161cb8359ef56e76e0607459d6280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848353"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Rozšířené možnosti konfigurace rozšíření NPS pro službu Multi-Factor Authentication

Rozšíření Server síťových zásad (NPS) rozšiřuje vaše cloudové funkce Azure Multi-Factor Authentication do místní infrastruktury. Tento článek předpokládá, že již máte nainstalované rozšíření a nyní chcete vědět, jak přizpůsobit rozšíření pro potřeby. 

## <a name="alternate-login-id"></a>Alternativní přihlašovací ID

Vzhledem k tomu, že rozšíření NPS se připojuje k místním i cloudovým adresářům, může dojít k problému, kdy místní hlavní názvy uživatelů (UPN) neodpovídají názvům v cloudu. Chcete-li tento problém vyřešit, použijte alternativní přihlašovací ID. 

V rámci rozšíření NPS můžete určit atribut služby Active Directory, který se použije místo hlavního názvového okruhu ONN pro vícefaktorové ověřování Azure. To vám umožní chránit místní prostředky pomocí dvoustupňového ověření bez úprav místních upnů. 

Chcete-li nakonfigurovat alternativní přihlašovací `HKLM\SOFTWARE\Microsoft\AzureMfa` ID, přejděte na následující hodnoty registru a upravte je:

| Name (Název) | Typ | Výchozí hodnota | Popis |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | řetězec | Prázdné | Určete název atributu Služby Active Directory, který chcete použít místo hlavního názvu služby. Tento atribut se používá jako atribut AlternateLoginId. Pokud je tato hodnota registru nastavena na [platný atribut služby Active Directory](https://msdn.microsoft.com/library/ms675090.aspx) (například mail nebo displayName), použije se hodnota atributu místo hlavního názvu uživatele pro ověřování. Pokud je tato hodnota registru prázdná nebo není nakonfigurována, je zakázáno id aplikace AlternateLoginId a pro ověřování se použije hlavní název uživatele. |
| LDAP_FORCE_GLOBAL_CATALOG | Boolean | False | Tento příznak slouží k vynucení použití globálního katalogu pro vyhledávání LDAP při vyhledávání alternateloginid. Nakonfigurujte řadič domény jako globální katalog, přidejte atribut AlternateLoginId do globálního katalogu a povolte tento příznak. <br><br> Pokud je LDAP_LOOKUP_FORESTS nakonfigurován (není prázdný), **je tento příznak vynucen jako true**, bez ohledu na hodnotu nastavení registru. V takovém případě rozšíření NPS vyžaduje globální katalog, který má být nakonfigurován s alternateloginid atribut pro každou doménovou strukturu. |
| LDAP_LOOKUP_FORESTS | řetězec | Prázdné | Zadejte seznam doménových struktur oddělených středníkem, který chcete vyhledat. Například *contoso.com;foobar.com*. Pokud je tato hodnota registru nakonfigurována, rozšíření NPS iterativně prohledá všechny doménové struktury v pořadí, ve kterém byly uvedeny, a vrátí první úspěšnou hodnotu AlternateLoginId. Pokud tato hodnota registru není nakonfigurována, vyhledávání AlternateLoginId je omezeno na aktuální doménu.|

Chcete-li vyřešit problémy s alternativními přihlašovacími ID, použijte doporučené kroky pro [chyby alternativního přihlášení .](howto-mfa-nps-extension-errors.md#alternate-login-id-errors)

## <a name="ip-exceptions"></a>Výjimky IP

Pokud potřebujete sledovat dostupnost serveru, například pokud správci zatížení ověřují, které servery jsou spuštěny před odesláním úloh, nechcete, aby tyto kontroly byly blokovány požadavky na ověření. Místo toho vytvořte seznam adres IP, o kterých víte, že jsou používány účty služeb, a zakažte požadavky na vícefaktorové ověřování pro tento seznam.

Chcete-li nakonfigurovat seznam `HKLM\SOFTWARE\Microsoft\AzureMfa` povolených adres IP, přejděte na následující hodnotu registru a nakonfigurujte ji:

| Name (Název) | Typ | Výchozí hodnota | Popis |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | řetězec | Prázdné | Zadejte seznam adres IP oddělených středníkem. Zahrňte IP adresy počítačů, ze kterých pocházejí žádosti o služby, například server NAS/VPN. Rozsahy IP adres a podsítě nejsou podporovány. <br><br> Například *10.0.0.1;10.0.0.2;10.0.0.3*.

> [!NOTE]
> Tento klíč registru není ve výchozím nastavení vytvořen instalačním programem a při restartování služby se v protokolu AuthZOptCh zobrazí chyba. Tato chyba v protokolu může být ignorována, ale pokud je tento klíč registru vytvořen a ponechán prázdný, pokud není potřeba, chybová zpráva se nevrátí.

Pokud požadavek přichází z IP adresy, `IP_WHITELIST`která existuje v , dvoustupňové ověření je přeskočeno. Seznam IP je porovnán s ip adresou, která je uvedena v atributu *ratNASIPAddress* požadavku RADIUS. Pokud požadavek RADIUS přichází bez atributu ratNASIPAddress, je zaznamenáno následující upozornění: "P_WHITE_LIST_WARNING::IP Whitelist je ignorován, protože v požadavku RADIUS v atributu NasIpAddress chybí zdrojová adresa IP."

## <a name="next-steps"></a>Další kroky

[řešení chybových zpráv z rozšíření NPS pro Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)
