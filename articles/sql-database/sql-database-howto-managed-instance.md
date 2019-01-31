---
title: Jak nakonfigurovat službu Azure SQL Database managed instance | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat a spravovat spravovanou instanci Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: howto
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: carlr
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: 91a9f6c4b9763c3261307eb9512f0db9d6aefa89
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55477756"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Jak používat ve službě Azure SQL Database managed instance

V tomto článku najdete různé průvodce, skripty a vysvětlení, které vám mohou pomoci při správě a konfiguraci spravované instance.

## <a name="migration"></a>Migrace

- [Migrace do spravované instance](sql-database-managed-instance-migrate.md) – přečtěte si víc o doporučených migrace procesů a nástrojů pro migraci do spravované instance.

- [Transparentní šifrování dat certifikátu migrovat do spravované instance](sql-database-managed-instance-migrate-tde-certificate.md) – Pokud je databáze systému SQL Server je chráněné pomocí transparentního šifrování dat (TDE), potřebujete migrovat certifikát, který managed instance můžete použít k dešifrování zálohu, kterou chcete obnovit v Azure.

## <a name="network-configuration"></a>Konfigurace sítě

- [Určit velikost spravované instance podsítě](sql-database-managed-instance-determine-size-vnet-subnet.md) – spravovaná instance je umístěn v vyhradí podsíť, která nelze změnit velikost, jakmile přidáte spolehlivým prostředkům. Proto je třeba k výpočtu, jaký rozsah IP adres by byla zapotřebí pro podsítě v závislosti na počtu a typů instancí, které chcete nasadit v podsíti.
- [Vytvořit novou virtuální síť a podsíť pro spravovanou instanci](sql-database-managed-instance-create-vnet-subnet.md) – virtuální síť Azure a podsíť, ve které chcete nasadit spravované instance musí být nakonfigurované podle [je zde popsáno, požadavky na síťovou](sql-database-managed-instance-connectivity-architecture.md#network-requirements). V této příručce nenajdete nejjednodušší způsob, jak vytvořit novou virtuální síť a podsítě správně nakonfigurovaný pro spravované instance.
- [Konfigurace stávající virtuální síť a podsíť pro spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md) – Pokud chcete provést konfiguraci stávající virtuální síť a podsíť k nasazení spravované instance v rámci, tady najdete skript, který kontroluje, [požadavky na síťovou](sql-database-managed-instance-connectivity-architecture.md#network-requirements) a ujistěte se nakonfiguruje podle požadavků vaší podsítě.
- [Konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md) – je potřeba nakonfigurovat vlastní DNS, pokud chcete přístup k externím prostředkům ve vlastních doménách z vaší spravované instance přes odkazovaný server db profily e-mailu.
- [Synchronizace konfigurace sítě](sql-database-managed-instance-sync-network-configuration.md) – se může stát, že i když jste [vaší aplikace integrované s Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), můžete&#39;t navázat připojení k managed instance. Jednou z věcí, které můžete zkusit se aktualizovat konfiguraci sítě pro váš plán služby.
- [Vyhledat IP adresu koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md) – spravovaná instance používá veřejného koncového bodu správy – pouze pro účely. Můžete určit IP adresu koncového bodu správy pomocí skriptů je popsáno zde.
- [Ověření integrované firewall ochrany](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – spravovaná instance je pak chráněn rozhraním integrovanou bránu firewall, které umožní provoz pouze nezbytné porty. Můžete zkontrolovat a ověření integrované brány firewall pravidla pomocí skriptů je popsáno v této příručce.
- [Připojení aplikací](sql-database-managed-instance-connect-app.md) – spravovaná instance je umístěn ve vaší vlastní privátní virtuální sítě Azure s privátní IP adresu. Další informace o různých schématech připojení aplikací k vaší spravované instance.

## <a name="feature-configuration"></a>Konfigurace funkcí

- [Transakční replikace](replication-with-sql-database-managed-instance.md) umožňuje replikovat data mezi spravované instance nebo z místních SQL serveru do managed instance a naopak. Najdete další informace o tom, jak používat a konfigurovat replikaci transakcí v této příručce.
- [Konfigurace zjišťování hrozeb](sql-database-managed-instance-threat-detection.md) – [detekce hrozeb](sql-database-threat-detection-overview.md) je integrovaná funkce Azure SQL Database, který zjistí různých potenciální útoky, jako jsou Injektáž SQL nebo přístupu z podezřelých míst. V této příručce můžete zjistěte, jak povolit a konfigurovat [detekce hrozeb](sql-database-threat-detection-overview.md) pro spravovanou instanci.

## <a name="next-steps"></a>Další postup

- Další informace o [provede postupy pro izolované databáze](sql-database-howto-single-database.md)