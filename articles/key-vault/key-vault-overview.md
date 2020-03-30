---
title: Azure Key Vault – přehled – trezor klíčů Azure | Dokumenty společnosti Microsoft
description: Azure Key Vault je cloudová služba, která funguje jako zabezpečené úložiště tajných klíčů.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 4e2953b107b017d032e737e2878472166c677839
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78194950"
---
# <a name="what-is-azure-key-vault"></a>Co je Azure Key Vault?

Azure Key Vault pomáhá vyřešit následující problémy:

- **Správa tajných klíčů** – Azure Key Vault je možné využít k zabezpečenému ukládání tokenů, hesel, certifikátů, klíčů rozhraní API a dalších tajných klíčů a důsledné kontrole přístupu k nim.
- **Správa klíčů** – Azure Key Vault se dá použít taky jako řešení pro správu klíčů. Azure Key Vault usnadňuje vytváření a správu šifrovacích klíčů sloužících k šifrování dat. 
- **Správa certifikátů** – Azure Key Vault je také služba, která umožňuje snadno zřídit, spravovat a nasazovat veřejné a soukromé certifikáty zabezpečení/zabezpečených soketů (TLS/SSL) pro použití s Azure a interními připojenými prostředky. 
- **Ukládat tajné kódy podporované moduly hardwarového zabezpečení** – tajné klíče a klíče mohou být chráněny softwarem nebo ověřenými moduly hardwarového zabezpečení úrovně 2 FIPS 140-2

## <a name="why-use-azure-key-vault"></a>Proč používat Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizace tajných klíčů aplikací

Centralizace ukládání tajných klíčů aplikací ve službě Azure Key Vault umožňuje řídit jejich distribuci. Key Vault výrazně snižuje riziko nechtěného úniku tajných klíčů. Při použití služby Key Vault už vývojáři aplikací nemusejí ukládat informace o zabezpečení ve svých aplikacích. Není nutné ukládat informace o zabezpečení v aplikacích eliminuje potřebu, aby tyto informace součástí kódu. Aplikace se například může potřebovat připojit k databázi. Místo ukládání připojovacího řetězce v kódu aplikace ho můžete bezpečně uložit do trezoru klíčů.

Vaše aplikace mohou bezpečně přistupovat k informacím, které potřebují, pomocí identifikátorů URI. Tyto identifikátory URI umožňují aplikacím načíst konkrétní verze tajného klíče. Není nutné psát vlastní kód k ochraně všech tajných informací uložených v trezoru klíčů.

### <a name="securely-store-secrets-and-keys"></a>Bezpečné ukládání tajných klíčů a klíčů

Tajné kódy a klíče jsou chráněné systémem Azure pomocí standardních algoritmů, délek klíčů a modulů hardwarového zabezpečení (HSM). Použité moduly HMS jsou ověřené podle standardu FIPS (Federal Information Processing Standards) 140-2 Level 2.

Pro přístup k trezoru klíčů se vyžaduje řádné ověření a autorizace volajícího (uživatel nebo aplikace). Ověření určí identitu volajícího a autorizace následně určí, které operace má volající povoleno provádět.

Ověření se provádí prostřednictvím Azure Active Directory. Autorizace se může provádět prostřednictvím řízení přístupu na základě role (RBAC) nebo zásad přístupu trezoru klíčů. RBAC se používá při správě trezorů a zásady přístupu trezoru klíčů se používají při pokusu o přístup k datům uloženým v trezoru.

Trezory klíčů Azure můžou být chráněné softwarovým nebo hardwarovým modulem HSM. Pro situace, kdy potřebujete lepší kontrolu, můžete v modulech hardwarového zabezpečení (HSM) importovat nebo generovat klíče, které nikdy neopustí hranice HSM. Společnost Microsoft používá moduly hardwarového zabezpečení nCipher. Pomocí nástrojů nCipher můžete přesunout klíč z vašeho hsm do trezoru klíčů Azure.

Služba Azure Key Vault je navržená tak, aby Microsoft vaše data neviděl ani je nemohl extrahovat.

### <a name="monitor-access-and-use"></a>Monitorování přístupu a využití

Po vytvoření několika trezorů klíčů budete chtít monitorovat, jak a kdy se k vašim klíčům a tajným klíčům přistupuje. Aktivitu můžete sledovat povolením protokolování pro vaše trezory. Ve službě Azure Key Vault můžete nakonfigurovat následující:

- Archivace do účtu úložiště
- Streamování do centra událostí
- Odešlete protokoly do protokolů Azure Monitor.

Máte kontrolu nad svými protokoly, které můžete zabezpečit prostřednictvím omezení přístupu, a můžete také odstranit protokoly, které už nepotřebujete.

### <a name="simplified-administration-of-application-secrets"></a>Zjednodušená správa tajných klíčů aplikací

Pokud ukládáte cenná data, musíte provést několik kroků. Bezpečnostní informace musí být zabezpečeny, musí sledovat životní cyklus a musí být vysoce dostupné. Azure Key Vault zjednodušuje proces plnění těchto požadavků tím, že:

- Odstranění nutnosti interních znalostí modulů hardwarového zabezpečení.
- Rychle vertikálně navyšuje kapacitu s ohledem na špičky využití ve vaší organizaci.
- Replikuje obsah služby Key Vault v jedné oblasti do sekundární oblasti. Replikace dat zajišťuje vysokou dostupnost a bere pryč potřebu jakékoli akce od správce k aktivaci převzetí služeb při selhání.
- Poskytuje standardní možnosti správy Azure prostřednictvím portálu, Azure CLI nebo PowerShellu.
- Automatizuje určité úlohy prováděné s certifikáty, které jste zakoupili od veřejných certifikačních autorit, třeba jejich registraci a obnovení.

Kromě toho trezory klíčů Azure umožňují oddělení tajných klíčů aplikací. Aplikace mohou přistupovat pouze k trezoru, ke kterému mají povolen přístup, a mohou být omezeny pouze na provádění určitých operací. Službu Azure Key Vault můžete vytvořit pro jednotlivé aplikace a omezit přístup k tajným klíčům uloženým ve službě Key Vault na konkrétní aplikaci a tým vývojářů.

### <a name="integrate-with-other-azure-services"></a>Integrace s ostatními službami Azure

Jako zabezpečené úložiště v Azure se trezor klíčů používá ke zjednodušení scénářů, jako jsou:
-  [Azure Disk Encryption](../security/fundamentals/encryption-overview.md)
-  [Vždy šifrované]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) funkce v SQL serveru a Azure SQL Database
- [Služba Azure App Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

Samotná služba Key Vault se může integrovat s účty úložiště, centry událostí a analytikou protokolů.

## <a name="next-steps"></a>Další kroky

- [Rychlý start: Vytvoření služby Azure Key Vault pomocí rozhraní příkazového řádku](quick-create-cli.md)
- [Konfigurace webové aplikace Azure pro čtení tajného klíče z trezoru klíčů](tutorial-web-application-keyvault.md)
