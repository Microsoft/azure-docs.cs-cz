---
title: Přehled služby Azure Key Vault – Azure Key Vault | Dokumentace Microsoftu
description: Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných klíčů.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 34af20ee-3fa7-4f28-9d98-6168b1759764
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: 474148289bb03088bbf92eeadc4049ce19625e3e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56115942"
---
# <a name="what-is-azure-key-vault"></a>Co je Azure Key Vault?

Služba Azure Key Vault vám pomůže vyřešit následující problémy:

- **Správa tajných klíčů** – Azure Key Vault je možné využít k zabezpečenému ukládání tokenů, hesel, certifikátů, klíčů rozhraní API a dalších tajných klíčů a důsledné kontrole přístupu k nim.
- **Správa klíčů** – Azure Key Vault se dá použít taky jako řešení pro správu klíčů. Azure Key Vault usnadňuje vytváření a správu šifrovacích klíčů sloužících k šifrování dat. 
- **Správa certifikátů** – Azure Key Vault je taky služba, která umožňuje snadné zřizování, správu a nasazování veřejných a privátních certifikátů SSL/TLS (Secure Sockets Layer/Transport Layer Security) pro použití s Azure a interními připojenými prostředky. 
- **Ukládání tajných klíčů chráněných moduly hardwarového zabezpečení (HSM)** – klíče a tajné klíče je možné chránit pomocí softwaru nebo modulů HSM ověřených podle standardu FIPS 140-2 Level 2.

## <a name="why-use-azure-key-vault"></a>Proč používat Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizace tajných klíčů aplikací

Centralizace ukládání tajných klíčů aplikací ve službě Azure Key Vault umožňuje řídit jejich distribuci. Key Vault výrazně snižuje riziko nechtěného úniku tajných klíčů. Při použití služby Key Vault už vývojáři aplikací nemusejí ukládat informace o zabezpečení ve svých aplikacích. Není potřeba ukládat informace o zabezpečení v aplikacích eliminuje potřeba zahrnout tyto informace kódu. Aplikace se například může potřebovat připojit k databázi. Místo uložení připojovacího řetězce v kódu aplikace, můžete ho uložit bezpečně ve službě Key Vault.

Vaše aplikace může zabezpečený přístup k informacím, stačí pomocí identifikátorů URI. Tyto identifikátory URI povolit aplikace, které chcete načíst konkrétní verzi tajného kódu. Není nutné psát vlastní kód pro zajištění ochrany tajných informací uložených v Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Bezpečné ukládání tajných klíčů a klíčů

Tajné kódy a klíče jsou chráněné systémem Azure pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení (HSM). Použité moduly HMS jsou ověřené podle standardu FIPS (Federal Information Processing Standards) 140-2 Level 2.

Pro přístup k trezoru klíčů se vyžaduje řádné ověření a autorizace volajícího (uživatel nebo aplikace). Ověření určí identitu volajícího a autorizace následně určí, které operace má volající povoleno provádět.

Ověření se provádí prostřednictvím Azure Active Directory. Autorizace se může provádět prostřednictvím řízení přístupu na základě role (RBAC) nebo zásad přístupu trezoru klíčů. RBAC se používá při správě trezorů a zásady přístupu trezoru klíčů se používají při pokusu o přístup k datům uloženým v trezoru.

Trezory klíčů Azure můžou být chráněné softwarovým nebo hardwarovým modulem HSM. Pro situace, kdy potřebujete lepší kontrolu, můžete v modulech hardwarového zabezpečení (HSM) importovat nebo generovat klíče, které nikdy neopustí hranice HSM. Microsoft využívá moduly hardwarového zabezpečení Thales. K přesunu klíče z vašeho HSM do služby Azure Key Vault můžete použít nástroje Thales.

Služba Azure Key Vault je navržená tak, aby Microsoft vaše data neviděl ani je nemohl extrahovat.

### <a name="monitor-access-and-use"></a>Monitorování přístupu a využití

Po vytvoření několika trezorů klíčů budete chtít monitorovat, jak a kdy se k vašim klíčům a tajným klíčům přistupuje. Aktivitu můžete sledovat povolením protokolování pro vaše trezory. Ve službě Azure Key Vault můžete nakonfigurovat následující:

- Archivace do účtu úložiště
- Streamování do centra událostí
- Odesílání protokolů do Log Analytics

Máte kontrolu nad svými protokoly, které můžete zabezpečit prostřednictvím omezení přístupu, a můžete také odstranit protokoly, které už nepotřebujete.

### <a name="simplified-administration-of-application-secrets"></a>Zjednodušená správa tajných klíčů aplikací

Pokud ukládáte cenná data, musíte provést několik kroků. Informace o zabezpečení musí být zabezpečená, musí podstoupit životní cyklus, musí být vysoce dostupný. Služba Azure Key Vault zjednodušuje celý proces od splnění těchto požadavků:

- Odstraňuje potřebu interní znalosti modulů hardwarového zabezpečení.
- Rychle vertikálně navyšuje kapacitu s ohledem na špičky využití ve vaší organizaci.
- Replikuje obsah služby Key Vault v jedné oblasti do sekundární oblasti. Replikace dat zajišťuje vysokou dostupnost a eliminuje potřebu jakékoli akce od správce k aktivaci převzetí služeb při selhání.
- Poskytuje standardní možnosti správy Azure prostřednictvím portálu, Azure CLI nebo PowerShellu.
- Automatizuje určité úlohy prováděné s certifikáty, které jste zakoupili od veřejných certifikačních autorit, třeba jejich registraci a obnovení.

Kromě toho trezory klíčů Azure umožňují oddělení tajných klíčů aplikací. Aplikace můžou přistupovat pouze k trezoru, můžou získat přístup, a je možné provádět pouze určité operace. Službu Azure Key Vault můžete vytvořit pro jednotlivé aplikace a omezit přístup k tajným klíčům uloženým ve službě Key Vault na konkrétní aplikaci a tým vývojářů.

### <a name="integrate-with-other-azure-services"></a>Integrace s ostatními službami Azure

Jako zabezpečené úložiště v Azure Key Vault používá k zjednodušení scénářů, jako jsou:
-  [Azure Disk Encryption](../security/azure-security-disk-encryption.md)
-  [s funkcí always encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkce v systému SQL server a Azure SQL Database
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/web-sites-purchase-ssl-web-site). 

Samotná služba Key Vault se může integrovat s účty úložiště, centry událostí a Log Analytics.

## <a name="next-steps"></a>Další postup

- [Rychlé zprovoznění: Vytvoření služby Azure Key Vault pomocí rozhraní příkazového řádku](quick-create-cli.md)
- [Konfigurace webové aplikace Azure pro čtení tajného klíče ze služby Key Vault](tutorial-web-application-keyvault.md)
