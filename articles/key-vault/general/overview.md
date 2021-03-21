---
title: Přehled Azure Key Vault – Azure Key Vault
description: Azure Key Vault je zabezpečené úložiště tajných kódů, které poskytuje správu tajných klíčů, klíčů a certifikátů, a to všechno, co jsou zajištěné moduly hardwarového zabezpečení.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: overview
ms.custom: mvc
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 4747c958b5e592458c14bbf4244953564c252678
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98790119"
---
# <a name="about-azure-key-vault"></a>Informace o službě Azure Key Vault

Azure Key Vault pomáhá vyřešit následující problémy:

- **Správa tajných klíčů** – Azure Key Vault je možné využít k zabezpečenému ukládání tokenů, hesel, certifikátů, klíčů rozhraní API a dalších tajných klíčů a důsledné kontrole přístupu k nim.
- **Správa klíčů** – Azure Key Vault se dá použít taky jako řešení pro správu klíčů. Azure Key Vault usnadňuje vytváření a správu šifrovacích klíčů sloužících k šifrování dat. 
- **Správa certifikátů** – Azure Key Vault je taky služba, která umožňuje snadno zřídit, spravovat a nasazovat veřejné a soukromé certifikáty TLS/SSL (Transport Layer Security/SSL (Secure Sockets Layer)) pro použití s Azure a vašimi interními připojenými prostředky.

Azure Key Vault má dvě úrovně služeb: Standard, který šifruje pomocí softwarového klíče a úrovně Premium, která zahrnuje klíče chráněné modulem hardwarového zabezpečení (HSM). Porovnání mezi úrovněmi Standard a Premium najdete na [stránce s cenami Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="why-use-azure-key-vault"></a>Proč používat Azure Key Vault?

### <a name="centralize-application-secrets"></a>Centralizace tajných klíčů aplikací

Centralizace ukládání tajných klíčů aplikací ve službě Azure Key Vault umožňuje řídit jejich distribuci. Key Vault výrazně snižuje riziko nechtěného úniku tajných klíčů. Při použití služby Key Vault už vývojáři aplikací nemusejí ukládat informace o zabezpečení ve svých aplikacích. Není nutné ukládat informace o zabezpečení v aplikacích eliminuje nutnost udělat si tuto část kódu. Aplikace se například může potřebovat připojit k databázi. Místo uložení připojovacího řetězce v kódu aplikace ho můžete bezpečně uložit do Key Vault.

Vaše aplikace můžou zabezpečeně přistupovat k informacím, které potřebují, pomocí identifikátorů URI. Tyto identifikátory URI umožňují aplikacím načíst konkrétní verze tajného kódu. Není nutné psát vlastní kód pro ochranu jakýchkoli tajných informací uložených v Key Vault.

### <a name="securely-store-secrets-and-keys"></a>Bezpečné ukládání tajných klíčů a klíčů

Pro přístup k trezoru klíčů se vyžaduje řádné ověření a autorizace volajícího (uživatel nebo aplikace). Ověření určí identitu volajícího a autorizace následně určí, které operace má volající povoleno provádět.

Ověření se provádí prostřednictvím Azure Active Directory. Autorizaci je možné provádět prostřednictvím řízení přístupu na základě role Azure (RBAC) nebo zásad Key Vault přístupu. Pokud se při pokusu o přístup k datům uloženým v trezoru používá Správa trezorů a zásad přístupu trezoru klíčů, používá se Azure RBAC.

Trezory klíčů Azure můžou být chráněné softwarem nebo, s Azure Key Vault Premium, hardwaremi chráněnými moduly hardwarového zabezpečení (HSM). Klíče chráněné softwarem, tajné klíče a certifikáty jsou chráněny Azure pomocí standardních algoritmů a délek klíčů.  V situacích, kdy potřebujete přidané záruky, můžete importovat nebo generovat klíče v HSM, které nikdy nezanechají hranici HSM. Azure Key Vault používá podpůrný software nCipher HSM, které jsou ověřené ve standardu FIPS (Federal Information Processing Standards) 140-2 úrovně 2. Pomocí nástrojů podpůrný software nCipher můžete přesunout klíč ze svého modulu HARDWAROVÉho zabezpečení do Azure Key Vault.

Služba Azure Key Vault je navržená tak, aby Microsoft vaše data neviděl ani je nemohl extrahovat.

### <a name="monitor-access-and-use"></a>Monitorování přístupu a využití

Po vytvoření několika trezorů klíčů budete chtít monitorovat, jak a kdy se k vašim klíčům a tajným klíčům přistupuje. Aktivitu můžete sledovat povolením protokolování pro vaše trezory. Ve službě Azure Key Vault můžete nakonfigurovat následující:

- Archivace do účtu úložiště
- Streamování do centra událostí
- Odešle protokoly do protokolů Azure Monitor.

Máte kontrolu nad svými protokoly, které můžete zabezpečit prostřednictvím omezení přístupu, a můžete také odstranit protokoly, které už nepotřebujete.

### <a name="simplified-administration-of-application-secrets"></a>Zjednodušená správa tajných klíčů aplikací

Pokud ukládáte cenná data, musíte provést několik kroků. Bezpečnostní informace musí být zabezpečené, musí následovat po životním cyklu a musí být vysoce dostupná. Azure Key Vault zjednodušuje proces splnění těchto požadavků:

- Odstranění nutnosti interních znalostí modulů hardwarového zabezpečení.
- Horizontální navýšení kapacity pro splnění špičky využití vaší organizace
- Replikuje obsah služby Key Vault v jedné oblasti do sekundární oblasti. Replikace dat zajišťuje vysokou dostupnost a trvá od správce k aktivaci převzetí služeb při selhání potřebnou akci.
- Poskytuje standardní možnosti správy Azure prostřednictvím portálu, Azure CLI nebo PowerShellu.
- Automatizuje určité úlohy prováděné s certifikáty, které jste zakoupili od veřejných certifikačních autorit, třeba jejich registraci a obnovení.

Kromě toho trezory klíčů Azure umožňují oddělení tajných klíčů aplikací. Aplikace mohou přistupovat pouze k trezoru, ke kterému mají povolen přístup, a mohou být omezeny pouze na provádění konkrétních operací. Službu Azure Key Vault můžete vytvořit pro jednotlivé aplikace a omezit přístup k tajným klíčům uloženým ve službě Key Vault na konkrétní aplikaci a tým vývojářů.

### <a name="integrate-with-other-azure-services"></a>Integrace s ostatními službami Azure

V Azure se jako zabezpečené úložiště používá Key Vault k zjednodušení podobných scénářů:
-  [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md)
-  Funkce [Always Encrypted]( https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine) a [transparentní šifrování dat]( https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-ver15) v systému SQL Server a Azure SQL Database
- [Azure App Service]( https://docs.microsoft.com/azure/app-service/configure-ssl-certificate). 

Samotná služba Key Vault se může integrovat s účty úložiště, centry událostí a analytikou protokolů.

## <a name="next-steps"></a>Další kroky

- Další informace o [klíčích, tajných klíčích a certifikátech](about-keys-secrets-certificates.md)
- [Rychlý start: Vytvoření služby Azure Key Vault pomocí rozhraní příkazového řádku](../secrets/quick-create-cli.md)
- [Ověřování, žádosti a odpovědi](../general/authentication-requests-and-responses.md)
- [Průvodce vývojáře pro službu Key Vault](../general/developers-guide.md)
