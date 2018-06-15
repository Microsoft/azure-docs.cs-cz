---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9fa18b14b82376a25bb434acd770d340b1ef9262
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30197030"
---
Pokud máte potíže s připojením, zkontrolujte následující položky:

- Pokud jste exportovali klientský certifikát, ujistěte se, že jste ho exportovali jako soubor .pfx s výchozím nastavením Zahrnout všechny certifikáty na cestě k certifikátu, pokud je to možné. Při exportu s použitím této hodnoty se exportují také informace o kořenovém certifikátu. Při instalaci certifikátu na klientský počítač se na něj nainstaluje i kořenový certifikát obsažený v souboru .pfx. Na klientském počítači musí být nainstalované informace o kořenovém certifikátu. Můžete to ověřit tak, že otevřete **správu uživatelských certifikátů** a přejdete do umístění **Důvěryhodné kořenové certifikační autority\Certifikáty**. Zkontrolujte, že je kořenový certifikát uvedený. Aby ověřování fungovalo, musí být kořenový certifikát přítomný.

- Pokud používáte certifikát vydaný řešením CA organizace a máte problémy s ověřováním, zkontrolujte pořadí ověřování na klientském certifikátu. Pořadí seznamu ověřování můžete zkontrolovat dvojím kliknutím na klientský certifikát a přechodem na **Podrobnosti > Použití rozšířeného klíče**. Ujistěte se, že je na prvním místě seznamu položka Ověření klienta. Pokud tomu tak není, musíte vydat klientský certifikát založený na šabloně uživatele, která má Ověření klienta jako první položku seznamu.

- Další informace o řešení potíží s P2S najdete v tématu [Řešení potíží s připojeními P2S](../articles/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).