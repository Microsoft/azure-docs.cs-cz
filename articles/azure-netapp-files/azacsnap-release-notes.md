---
title: Poznámky k verzi pro nástroj pro snímek konzistentní vzhledem k aplikacím Azure pro Azure NetApp Files | Microsoft Docs
description: Poskytuje poznámky k verzi nástroje pro snímek konzistentní vzhledem k aplikacím Azure, které můžete použít s Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: phjensen
ms.openlocfilehash: 01fb93dcd0a1d5c1db615c47d7811a0baa863c9b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105111430"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool-preview"></a>Poznámky k verzi pro nástroj snímek konzistentní vzhledem k aplikacím Azure (Preview)

Tato stránka obsahuje seznam hlavních změn provedených v AzAcSnap a poskytování nových funkcí nebo řešení vad.

## <a name="march-2021"></a>Březen – 2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v 5.0 Preview (sestavení: 20210318.30771)

AzAcSnap v 5.0 Preview (Build: 20210318.30771) byl vydán s následujícími opravami a vylepšeními:

- Odebrali jsme nutnost přidat uživatele AZACSNAP do databáze tenanta SAP HANA, přečtěte si část [povolení komunikace s SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana) .
- Opravte, aby se povolilo [obnovení](azacsnap-cmd-ref-restore.md) se svazky nakonfigurovanými pomocí ruční technologie QoS.
- Byl přidán ovládací prvek mutex pro omezení připojení SSH pro velkou instanci Azure.
- Opravte instalační program pro zpracování názvů cest s mezerami a dalšími souvisejícími problémy.
- Při přípravě na podporu jiných databázových serverů změnil nepovinný parametr--hanasid na--dbsid.

Stáhněte si [nejnovější verzi](https://aka.ms/azacsnapdownload) instalačního programu a přečtěte si [, jak začít.](azacsnap-get-started.md)

## <a name="next-steps"></a>Další kroky

- [Začínáme s nástrojem pro vytváření snímků konzistentním s aplikacemi Azure](azacsnap-get-started.md)
