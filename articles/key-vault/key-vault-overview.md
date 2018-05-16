---
title: Přehled služby Azure Key Vault | Microsoft Docs
description: Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných klíčů.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 05/08/2018
ms.author: barclayn
ms.openlocfilehash: f9648e15c720c076a65e84a95f4160f27eec598d
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="what-is-azure-key-vault"></a>Co je Azure Key Vault?

Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných klíčů.

Pro zajištění provozu vašich aplikací máte potřebná hesla, připojovací řetězce a další informace. Chcete zajistit, aby tyto informace byly dostupné, ale zabezpečené. S tím vám může pomoct Azure Key Vault. Azure Key Vault vám může pomoct zabezpečeně ukládat a spravovat tajné klíče aplikací.

Key Vault vám umožní vytvořit několik zabezpečených kontejnerů označovaných jako trezory. Tyto trezory využívají moduly hardwarového zabezpečení (HSM). Trezory pomáhají snížit riziko nechtěné ztráty informací o zabezpečení tím, že centralizují ukládání tajných klíčů aplikací. Trezory klíčů také řídí a protokolují přístup ke všem položkám, které jsou v nich uložené. Azure Key Vault může zpracovávat žádosti o certifikáty TLS (Transport Layer Security) a jejich obnovování a poskytuje funkce potřebné pro robustní řešení správy životního cyklu certifikátů.

 Služba Azure Key Vault je navržená pro podporu klíčů a tajných klíčů aplikací. Služba Key Vault není určená k použití jako úložiště uživatelských hesel.

## <a name="why-use-azure-key-vault"></a>Proč používat Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizace tajných klíčů aplikací

Centralizace ukládání tajných klíčů aplikací ve službě Azure Key Vault umožňuje řídit jejich distribuci. Tím se výrazně snižuje riziko nechtěného úniku tajných klíčů. Při použití služby Key Vault už vývojáři aplikací nemusejí ukládat informace o zabezpečení ve svých aplikacích. Tím se eliminuje potřeba zahrnout tyto informace do kódu. Aplikace se například může potřebovat připojit k databázi. Místo uložení připojovacího řetězce v kódu aplikace ho můžete bezpečně uložit ve službě Key Vault.

Vaše aplikace můžou v případě potřeby bezpečně přistupovat k těmto informacím pomocí identifikátorů URI, které jim po uložení klíče nebo tajného klíče aplikace ve službě Azure Key Vault umožňují načíst konkrétní verzi tajného klíče. Toto se děje bez nutnosti psát vlastní kód pro zajištění ochrany tajných informací.

### <a name="securely-store-secrets"></a>Bezpečné ukládání tajných klíčů

Klíče jsou chráněné systémem Azure pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení (HSM). Použité moduly HMS jsou ověřené podle standardu FIPS (Federal Information Processing Standards) 140-2 Level 2.

Pro přístup k trezoru klíčů se vyžaduje řádné ověření a autorizace volajícího (uživatel nebo aplikace). Ověření určí identitu volajícího a autorizace následně určí, které operace má volající povoleno provádět.

Ověření se provádí prostřednictvím Azure Active Directory. Autorizace se může provádět prostřednictvím řízení přístupu na základě role (RBAC) nebo zásad přístupu trezoru klíčů. RBAC se používá při správě trezorů a zásady přístupu trezoru klíčů se používají při pokusu o přístup k datům uloženým v trezoru.

Trezory klíčů Azure můžou být chráněné softwarovým nebo hardwarovým modulem HSM. Pro situace, kdy potřebujete lepší kontrolu, můžete v modulech hardwarového zabezpečení (HSM) importovat nebo generovat klíče, které nikdy neopustí hranice HSM. Microsoft využívá moduly hardwarového zabezpečení Thales. K přesunu klíče z vašeho HSM do služby Azure Key Vault můžete použít nástroje Thales.

A konečně, služba Azure Key Vault je navržená tak, aby společnost Microsoft vaše klíče neznala ani neextrahovala.

### <a name="monitor-access-and-use"></a>Monitorování přístupu a využití

Po vytvoření několika trezorů klíčů budete chtít monitorovat, jak a kdy se k vašim klíčům a tajným klíčům přistupuje. Můžete to provést povolením protokolování služby Key Vault. Ve službě Azure Key Vault můžete nakonfigurovat následující:

- Archivace do účtu úložiště
- Streamování do centra událostí
- Odesílání protokolů do Log Analytics

Máte kontrolu nad svými protokoly, které můžete zabezpečit prostřednictvím omezení přístupu, a můžete také odstranit protokoly, které už nepotřebujete.

### <a name="simplified-administration-of-application-secrets"></a>Zjednodušená správa tajných klíčů aplikací

Pokud ukládáte cenná data, musíte provést několik kroků. Informace o zabezpečení musí být zabezpečené, odpovídat životnímu cyklu a musí být vysoce dostupné. Azure Key Vault spoustu toho zjednodušuje tím, že:

- Odstraňuje potřebu interní znalosti modulů HSM.
- Rychle vertikálně navyšuje kapacitu s ohledem na špičky využití ve vaší organizaci.
- Replikuje obsah služby Key Vault v jedné oblasti do sekundární oblasti. To zajišťuje vysokou dostupnost a eliminuje potřebu jakékoli akce od správce k aktivaci převzetí služeb při selhání.
- Poskytuje standardní možnosti správy Azure prostřednictvím portálu, Azure CLI nebo PowerShellu.
- Automatizuje určité úlohy, například registraci a obnovení, s certifikáty, které jste zakoupili od veřejných certifikačních autorit.

Kromě toho trezory klíčů Azure umožňují oddělení tajných klíčů aplikací. Aplikace můžou přistupovat pouze k trezoru, ke kterému mají povolený přístup, a provádět pouze určité operace. Službu Azure Key Vault můžete vytvořit pro jednotlivé aplikace a omezit přístup k tajným klíčům uloženým ve službě Key Vault na konkrétní aplikaci a tým vývojářů.

### <a name="integrate-with-other-azure-services"></a>Integrace s ostatními službami Azure

Služba Key Vault se jako zabezpečené úložiště v Azure používá k zjednodušení scénářů, jako je služba [Azure Disk Encryption](../security/azure-security-disk-encryption.md), funkce [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) v SQL Serveru a Azure SQL a [webové aplikace Azure]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). Samotná služba Key Vault se může integrovat s účty úložiště, centry událostí a Log Analytics.

## <a name="next-steps"></a>Další kroky

- [Rychlý start: Vytvoření služby Azure Key Vault pomocí rozhraní příkazového řádku](quick-create-cli.md)
- [Konfigurace webové aplikace Azure pro čtení tajného klíče ze služby Key Vault](tutorial-web-application-keyvault.md)