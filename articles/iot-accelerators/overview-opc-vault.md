---
title: Co je OPC trezor – Azure | Microsoft Docs
description: Tento článek poskytuje přehled trezoru OPC. Může nakonfigurovat, registrovat a spravovat životní cyklus certifikátů pro aplikace OPC UA v cloudu.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 715ed204e28d6260c28fa099b40fc78aa12de44d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281537"
---
# <a name="what-is-opc-vault"></a>Co je trezor OPC?

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

OPC trezor je mikroslužba, která umožňuje konfigurovat, registrovat a spravovat životní cyklus certifikátů pro server OPC UA a klientské aplikace v cloudu. Tento článek popisuje jednoduché případy použití trezoru OPC.

## <a name="certificate-management"></a>Správa certifikátů

Například výrobní společnost potřebuje připojit svůj počítač serveru OPC UA ke své nově sestavené klientské aplikaci. Když výrobce vytvoří počáteční přístup k serveru, zobrazí se v aplikaci serveru OPC UA okamžitě chybová zpráva s oznámením, že klientská aplikace není zabezpečená. Tento mechanismus je sestavený v počítači serveru OPC UA, aby nedocházelo k neoprávněnému přístupu k aplikacím, což zabrání v dílenském řízení začarovaný.

## <a name="application-security-management"></a>Správa zabezpečení aplikací
Specialista zabezpečení používá mikroslužbu OPC trezoru, která umožňuje snadno povolit serveru OPC UA komunikaci s jakoukoli klientskou aplikací, protože OPC trezor má všechny funkce pro Registry certifikátů, úložiště a správu životního cyklu. Server OPC UA je teď bezpečně připojený a může komunikovat s nově vytvořenou klientskou aplikací.

## <a name="the-complete-opc-vault-architecture"></a>Kompletní architektura trezoru OPC
Následující diagram znázorňuje kompletní architekturu trezoru OPC.

![Architektura úložiště OPC](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili o trezoru OPC a jeho použití, je tady doporučený další krok:

[Architektura úložiště OPC](overview-opc-vault-architecture.md)