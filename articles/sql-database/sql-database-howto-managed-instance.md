---
title: Konfigurace Azure SQL Database Managed Instance | Dokumentace Microsoftu
description: Zjistěte, jak konfigurovat a spravovat Azure SQL Database Managed Instance.
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
ms.date: 12/14/2018
ms.openlocfilehash: 10a33ac09b5cfa588bef88e6c1587d67036b1aef
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53440489"
---
# <a name="how-to-use-managed-instance"></a>Použití spravované Instance

V této části najdete různé průvodce, skripty a vysvětlení, které vám mohou pomoci při správě a konfiguraci Azure SQL Database – Managed Instance.

## <a name="migration"></a>Migrace

- [Migrace do spravované instance](sql-database-managed-instance-migrate.md) – přečtěte si víc o doporučených migrace procesů a nástrojů pro migraci do spravované Instance.

- [Transparentní šifrování dat certifikátu migraci do spravované instance](sql-database-managed-instance-migrate-tde-certificate.md) – Pokud je databáze systému SQL Server je chráněné pomocí transparentního šifrování dat (TDE), potřebujete migrovat certifikát, který Managed Instance můžete použít k dešifrování zálohu, kterou chcete obnovit v Azure.

## <a name="network-configuration"></a>Konfigurace sítě

- [Určit velikost podsíť Managed Instance](sql-database-managed-instance-determine-size-vnet-subnet.md) – Managed Instance je umístěn v vyhradí podsíť, která nelze změnit velikost, jakmile přidáte spolehlivým prostředkům. Proto je třeba k výpočtu, jaký rozsah IP adres by byla zapotřebí pro podsítě v závislosti na počtu a typů instancí, které chcete nasadit v podsíti.
- [Vytvořit novou virtuální síť a podsíť pro Managed Instance](sql-database-managed-instance-create-vnet-subnet.md) – virtuální síť Azure a podsíť, ve které chcete nasadit své Managed instance musí se nakonfigurovat podle postupů [je zde popsáno, požadavky na síťovou](sql-database-managed-instance-connectivity-architecture.md#network-requirements). V této příručce nenajdete nejjednodušší způsob, jak vytvořit nové virtuální sítě a podsítě správně nakonfigurovaný pro Managed instance.
- [Konfigurace stávající virtuální síť a podsíť pro Managed Instance](sql-database-managed-instance-configure-vnet-subnet.md) – Pokud chcete provést konfiguraci stávající virtuální síť a podsíť Managed instance v rámci nasazení, tady najdete skript, který kontroluje, [požadavky na síťovou](sql-database-managed-instance-connectivity-architecture.md#network-requirements) a Ujistěte se nakonfiguruje podle požadavků vaší podsítě.
- [Konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md) – je potřeba nakonfigurovat vlastní DNS, pokud chcete přístup k externím prostředkům ve vlastních doménách z Managed Instance prostřednictvím odkazovaný server db profily e-mailu.
- [Synchronizace konfigurace sítě](sql-database-managed-instance-sync-network-configuration.md) – se může stát, že i když jste [vaší aplikace integrované s Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md), můžete&#39;t navázat připojení k Managed Instance. Jednou z věcí, které můžete zkusit se aktualizovat konfiguraci sítě pro váš plán služby.
- [Vyhledat IP adresu koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md) – Managed Instance pomocí veřejného koncového bodu správy – pouze pro účely. Můžete určit IP adresu koncového bodu správy pomocí skriptů je popsáno zde.
- [Ověření integrované firewall ochrany](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – Managed Instance je pak chráněn rozhraním integrovanou bránu firewall, které umožní provoz pouze nezbytné porty. Můžete zkontrolovat a ověření integrované brány firewall pravidla pomocí skriptů je popsáno v této příručce.
- [Připojení aplikací](sql-database-managed-instance-connect-app.md) – Managed Instance je umístěn ve vaší vlastní privátní virtuální sítě Azure s privátní IP adresu. Další informace o různých schématech připojení aplikací k Managed Instance.

## <a name="feature-configuration"></a>Konfigurace funkcí

- [Transakční replikace](replication-with-sql-database-managed-instance.md) umožňuje replikovat data mezi spravovaných instancí, nebo z místního SQL serveru do Managed Instance a naopak. Najdete další informace o tom, jak používat a konfigurovat replikaci transakcí v této příručce.
- [Konfigurace zjišťování hrozeb](sql-database-managed-instance-threat-detection.md) – [detekce hrozeb](sql-database-threat-detection-overview.md) je integrovaná funkce Azure SQL Database, který zjistí různých potenciální útoky, jako jsou Injektáž SQL nebo přístupu z podezřelých míst. V této příručce můžete zjistěte, jak povolit a konfigurovat [detekce hrozeb](sql-database-threat-detection-overview.md) pro Managed Instance.

## <a name="next-steps"></a>Další postup
- Další informace o [postupy provede v izolované databáze](sql-database-howto-single-database.md)