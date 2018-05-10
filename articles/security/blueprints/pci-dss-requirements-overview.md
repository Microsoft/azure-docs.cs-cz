---
title: Azure platebních zpracování plán, podle kterého – přehled
description: Azure platebních zpracování plán, podle kterého - matice odpovědnosti zákazníka (přehled)
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 23cf68d8-bebd-4ac4-a194-39e052281c0e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 4c36f50b5c4ceba911003ec7a633dcab8724c6e0
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="pci-dss-requirements---high-level-overview"></a>Požadavky na PCI DSS – přehled

Karty oborový Standard zabezpečení dat platebních (PCI DSS) byla vyvinuta podněcovat a zvýšit zabezpečení dat držitele karty a usnadnění široký přijetí konzistentní data bezpečnostní opatření globálně. PCI DSS poskytuje základní úroveň technické a provozní požadavky určené k ochraně dat účet. PCI DSS se vztahuje na všechny entity účastní zpracování platebních karet, včetně obchodníků, procesory, příjemci, vystavitelů a poskytovatelé služeb. PCI DSS platí také pro všechny ostatní entity, které ukládat, zpracovat nebo přenášet data držitele karty (CHD) nebo ověřování citlivá data (SAD). Níže je souhrnné informace o požadavcích na 12 PCI DSS.

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

|   |   |
|---|---|
| **Tvorbu a udržování zabezpečený<br/>sítí a systémů** | 1. [Instalaci a údržbě konfiguraci brány firewall k ochraně dat držitele karty](pci-dss-requirement-1-firewall.md)<br/><br/> 2. [Nepoužívejte výchozí hodnoty poskytnuté dodavatelem systému hesel a dalších parametrů zabezpečení](pci-dss-requirement-2-password.md) |  
| **Ochrana dat držitele karty** | 3. [Chránit data uložená držitele karty](pci-dss-requirement-3-chd.md)<br/><br/> 4. [Šifrování přenosu dat držitele karty v otevřený, veřejných sítích](pci-dss-requirement-4-encryption.md) |
| **Udržovat ohrožení zabezpečení<br/>programu pro správu** | 5. [Chránit všechny systémy před malwarem a pravidelně ho aktualizovali antivirový software nebo programy](pci-dss-requirement-5-malware.md)<br/><br/> 6. [Vývoj a udržovat zabezpečených systémů a aplikací](pci-dss-requirement-6-secure-system.md) |
| **Implementovat přístup silné<br/>řízení míry** | 7. [Omezení přístupu k datům držitele karty podle obchodních potřeb vědět](pci-dss-requirement-7-access.md)<br/><br/> 8. [Identifikovat a ověřovat přístup k součástem systému](pci-dss-requirement-8-identity.md) <br/><br/> 9. [Omezte fyzický přístup k datům držitele karty](pci-dss-requirement-9-physical-access.md) |
| **Pravidelně sledujte a<br/>testovací sítě** | 10. [Sledování a monitorování veškerý přístup k síťovým prostředkům a data držitele karty](pci-dss-requirement-10-monitoring.md) <br/><br/> 11. [Pravidelně zkoušet zabezpečení systémy a procesy](pci-dss-requirement-11-testing.md) |
| **Udržovat informace<br/>zásady zabezpečení** | 12. [Udržovat zásadu, která řeší informace o zabezpečení pro všechny pracovníky](pci-dss-requirement-12-policy.md) |

