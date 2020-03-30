---
title: Konfigurace spravované instance
description: Přečtěte si, jak nakonfigurovat a spravovat spravovanou instanci Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 04/16/2019
ms.openlocfilehash: 906ae2a970ce1d5b82302d0277ca45bd93c23011
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256910"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Jak používat spravovanou instanci v Azure SQL Database

V tomto článku najdete různé příručky, skripty a vysvětlení, které vám pomohou spravovat a konfigurovat spravovanou instanci.

## <a name="migration"></a>Migrace

- [Migrace do spravované instance](sql-database-managed-instance-migrate.md) – Informace o doporučeném procesu migrace a nástrojích pro migraci do spravované instance.

- [Migrace certifikátu TDE do spravované instance](sql-database-managed-instance-migrate-tde-certificate.md) – Pokud je databáze serveru SQL Server chráněna transparentním šifrováním dat (TDE), budete muset migrovat certifikát, který může spravovaná instance použít k dešifrování zálohy, kterou chcete obnovit v Azure.

## <a name="network-configuration"></a>Konfigurace sítě

- [Určit velikost podsítě spravované instance](sql-database-managed-instance-determine-size-vnet-subnet.md) – Spravovaná instance je umístěna v podsíti, kterou nelze změnit po přidání prostředků uvnitř. Proto budete muset vypočítat, jaký rozsah IP adres by byl vyžadován pro podsíť v závislosti na počtu a typech instancí, které chcete nasadit v podsíti.
- [Vytvořte novou virtuální síť a podsíť pro spravovanou instanci](sql-database-managed-instance-create-vnet-subnet.md) – virtuální síť Azure a podsíť, kde chcete nasadit spravované instance, musí být nakonfigurované podle [požadavků na síť popsaných zde](sql-database-managed-instance-connectivity-architecture.md#network-requirements). V této příručce najdete nejjednodušší způsob, jak vytvořit novou virtuální síť a podsíť správně nakonfigurovanou pro spravované instance.
- [Konfigurace existující virtuální sítě a podsítě pro spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md) – pokud chcete nakonfigurovat existující virtuální síť a podsíť pro nasazení spravovaných instancí uvnitř, zde najdete skript, který kontroluje [požadavky na síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements) a umožňuje konfigurovat podsíť podle požadavků.
- [Konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md) – pokud chcete získat přístup k externím prostředkům ve vlastních doménách z vaší spravované instance prostřednictvím propojeného serveru profilů db mail, je třeba je nakonfigurovat vlastní DNS.
- [Synchronizace konfigurace sítě](sql-database-managed-instance-sync-network-configuration.md) – Může se stát, že i když jste [aplikaci integrovali do virtuální sítě Azure](../app-service/web-sites-integrate-with-vnet.md), můžete&#39;t navázat připojení ke spravované instanci. Jedna věc, kterou můžete zkusit, je aktualizovat konfiguraci sítě pro váš plán služeb.
- [Najít ip adresu koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md) – spravovaná instance používá veřejný koncový bod pro účely správy. Adresu IP koncového bodu správy můžete určit pomocí zde popsaného skriptu.
- [Ověřit vestavěnou ochranu brány firewall](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – Spravovaná instance je chráněna vestavěnou bránou firewall, která umožňuje přenos pouze na nezbytných portech. Vestavěná pravidla brány firewall můžete zkontrolovat a ověřit pomocí skriptu popsaného v této příručce.
- [Připojit aplikace](sql-database-managed-instance-connect-app.md) – spravovaná instance se umístí do vlastní privátní virtuální sítě Azure s privátní IP adresou. Přečtěte si o různých vzorech pro připojení aplikací ke spravované instanci.

## <a name="feature-configuration"></a>Konfigurace funkcí

- [Transakční replikace](replication-with-sql-database-managed-instance.md) umožňuje replikovat data mezi spravovanými instancemi nebo z místního serveru SQL Server na spravovanou instanci a naopak. Další informace o použití a konfiguraci replikace transakcí naleznete v této příručce.
- [Konfigurace detekce hrozeb](sql-database-managed-instance-threat-detection.md) – [detekce hrozeb](sql-database-threat-detection-overview.md) je integrovaná funkce Azure SQL Database, která detekuje různé potenciální útoky, jako je injektáž SQL injection nebo přístup z podezřelých umístění. V této příručce se dozvíte, jak povolit a nakonfigurovat [detekci hrozeb](sql-database-threat-detection-overview.md) pro spravovanou instanci.

## <a name="next-steps"></a>Další kroky

- Další informace o [návodech pro jednotlivé databáze](sql-database-howto-single-database.md)