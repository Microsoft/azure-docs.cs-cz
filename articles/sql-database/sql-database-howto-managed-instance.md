---
title: Postup konfigurace spravované instance
description: Naučte se konfigurovat a spravovat Azure SQL Database spravovanou instanci.
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
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811201"
---
# <a name="how-to-use-a-managed-instance-in-azure-sql-database"></a>Použití spravované instance v Azure SQL Database

V tomto článku najdete různé příručky, skripty a vysvětlení, které vám pomůžou se správou a konfigurací spravované instance.

## <a name="migration"></a>Migrace

- [Migrace na spravovanou instanci](sql-database-managed-instance-migrate.md) – Přečtěte si o doporučeném procesu migrace a nástrojích pro migraci do spravované instance.

- [Migrace TDE certifikátu na spravovanou instanci](sql-database-managed-instance-migrate-tde-certificate.md) – Pokud je vaše databáze SQL Server chráněná pomocí transparentního šifrování dat (TDE), budete muset migrovat certifikát, který spravovaná instance může použít k dešifrování zálohy, kterou chcete obnovit v Azure.

## <a name="network-configuration"></a>Konfigurace sítě

- [Určete velikost podsítě spravované instance](sql-database-managed-instance-determine-size-vnet-subnet.md) – spravovaná instance je umístěna ve vyhradení podsítě, kterou nelze po přidání prostředků do systému změnit. Proto byste museli počítat, jaký rozsah adres IP by se vyžadoval pro podsíť v závislosti na počtu a typech instancí, které chcete v podsíti nasadit.
- [Vytvoření nové virtuální sítě a podsítě pro spravovanou instanci](sql-database-managed-instance-create-vnet-subnet.md) – virtuální síť Azure a podsíť, kde chcete spravované instance nasadit, se musí nakonfigurovat podle [požadavků sítě](sql-database-managed-instance-connectivity-architecture.md#network-requirements), které jsou zde popsané. V této příručce najdete nejjednodušší způsob, jak vytvořit novou virtuální síť a podsíť správně nakonfigurovaných pro spravované instance.
- [Konfigurace existující virtuální sítě a podsítě pro spravovanou instanci](sql-database-managed-instance-configure-vnet-subnet.md) – Pokud chcete nakonfigurovat stávající virtuální síť a podsíť pro nasazení spravovaných instancí uvnitř, najdete tady skript, který kontroluje [požadavky na síť](sql-database-managed-instance-connectivity-architecture.md#network-requirements) a nakonfiguruje vaše podsíť podle požadavků.
- [Konfigurace vlastního DNS](sql-database-managed-instance-custom-dns.md) – Pokud chcete získat přístup k externím prostředkům v vlastní doméně ze spravované instance pomocí propojených serverových e-mailových profilů, musíte vlastní DNS nakonfigurovat.
- [Synchronizace konfigurace sítě](sql-database-managed-instance-sync-network-configuration.md) – může dojít k tomu, že i když jste [aplikaci integrovanou do Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md),&#39;můžete t navázat připojení ke spravované instanci. Jednou z věcí, které můžete vyzkoušet, je aktualizovat konfiguraci sítě pro svůj plán služby.
- [Najít IP adresu koncového bodu správy](sql-database-managed-instance-find-management-endpoint-ip-address.md) – spravovaná instance používá pro účely správy veřejný koncový bod. IP adresu koncového bodu správy můžete určit pomocí skriptu, který je zde popsán.
- [Ověřit integrovanou ochranu brány firewall](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md) – spravovaná instance je chráněná integrovanou bránou firewall, která umožňuje provoz pouze na nezbytných portech. Vestavěná pravidla brány firewall můžete zkontrolovat a ověřit pomocí skriptu popsaného v této příručce.
- [Připojení aplikací](sql-database-managed-instance-connect-app.md) – spravovaná instance je umístěná ve vaší soukromé virtuální síti Azure s privátní IP adresou. Přečtěte si o různých vzorech pro připojení aplikací ke spravované instanci.

## <a name="feature-configuration"></a>Konfigurace funkcí

- [Transakční replikace](replication-with-sql-database-managed-instance.md) umožňuje replikovat data mezi spravovanými instancemi nebo z místních SQL Server do spravované instance a naopak. Přečtěte si další informace o použití a konfiguraci replikace transakcí v této příručce.
- [Konfigurace detekce hrozeb](sql-database-managed-instance-threat-detection.md) – [detekce hrozeb](sql-database-threat-detection-overview.md) je integrovaná Azure SQL Database funkce, která detekuje různé potenciální útoky, jako je například vkládání SQL nebo přístup z podezřelých umístění. V této příručce se dozvíte, jak povolit a nakonfigurovat [detekci hrozeb](sql-database-threat-detection-overview.md) pro spravovanou instanci.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [průvodcích s jednotlivými databázemi](sql-database-howto-single-database.md) .