---
title: Protokol TLS 1,2 – Služba Azure Active Directory Registration Service
description: Odeberte podporu TLS 1,0 a 1,1 pro službu Azure AD Device Registration Service.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: reference
ms.date: 07/10/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2bb8c6c64e0a68f5176c4eb0c0177c5220394695
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89268753"
---
# <a name="enforce-tls-12-for-the-azure-ad-registration-service"></a>Vynutilit TLS 1,2 pro službu Azure AD Registration Service

Služba registrace zařízení Azure Active Directory (Azure AD) se používá k připojení zařízení ke cloudu s identitou zařízení. Služba Azure AD Device Registration Service aktuálně podporuje komunikaci s Azure pomocí protokolu TLS (Transport Layer Security) 1,2. Aby bylo zajištěno zabezpečení a nejlepší šifrování ve třídě, doporučuje společnost Microsoft zakázat TLS 1,0 a 1,1. Tento dokument obsahuje informace o tom, jak zajistit, aby počítače, které se používají k dokončení registrace a komunikovaly se službou Azure AD Device Registration Service, používaly protokol TLS 1,2.

Protokol TLS verze 1,2 je kryptografický protokol, který je navržený tak, aby poskytoval zabezpečenou komunikaci. Protokol TLS se zaměřuje především na zajištění ochrany osobních údajů a integrity dat. Protokol TLS přechází z mnoha iterací s verzí 1,2 definovaných v [dokumentu RFC 5246 (externí odkaz)](https://tools.ietf.org/html/rfc5246).

Aktuální analýza připojení zobrazuje málo použití protokolu TLS 1,1 a 1,0, ale tyto informace poskytujeme, abychom mohli aktualizovat všechny ovlivněné klienty nebo servery, které jsou potřebné, a to ještě před podporou TLS 1,1 a 1,0 konců. Pokud používáte jakoukoli místní infrastrukturu pro hybridní scénáře nebo Active Directory Federation Services (AD FS) (AD FS), ujistěte se, že infrastruktura podporuje příchozí i odchozí připojení, která používají protokol TLS 1,2.

## <a name="update-windows-servers"></a>Aktualizace serverů Windows

Pro Windows servery, které používají službu Azure AD Device Registration Service nebo fungují jako proxy, použijte následující postup, abyste zajistili, že je protokol TLS 1,2 povolený:

> [!IMPORTANT]
> Po aktualizaci registru je nutné restartovat systém Windows Server, aby se změny projevily.

### <a name="enable-tls-12"></a>Povolení protokolu TLS 1.2

Ujistěte se, že jsou nakonfigurované následující řetězce registru, jak je znázorněno níže:

- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ klient
  - "DisabledByDefault" = DWORD: 00000000
  - "Enabled" = DWORD: 00000001
- HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ Server
  - "DisabledByDefault" = DWORD: 00000000
  - "Enabled" = DWORD: 00000001
- HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\. NETFramework\v4.0.30319
  - "Do schusestrongcrypto" = DWORD: 00000001

## <a name="update-non-windows-proxies"></a>Aktualizovat proxy servery jiného typu než Windows

Všechny počítače, které fungují jako proxy servery mezi zařízeními a službou Azure AD Device Registration Service, musí zajistit, aby byl povolený protokol TLS 1,2. Zajistěte podporu podle pokynů dodavatele.

## <a name="update-ad-fs-servers"></a>Aktualizace AD FSch serverů

Všechny AD FS servery, které se používají ke komunikaci se službou Device Registration Service pro Azure AD, musí zajistit, aby byl povolený protokol TLS 1,2. Informace o tom, jak tuto konfiguraci povolit nebo ověřit, najdete v tématu [Správa protokolů SSL/TLS a šifrovacích sad pro AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs) .

## <a name="client-updates"></a>Aktualizace klienta

Vzhledem k tomu, že všechny kombinace klient-server a prohlížeče a serveru musí používat protokol TLS 1,2 pro připojení ke službě Azure AD Device Registration Service, možná budete muset aktualizovat tato zařízení.

U následujících klientů je známo, že nebude možné podporovat protokol TLS 1,2. Aktualizujte své klienty, abyste zajistili nepřerušený přístup.

- Android verze 4,3 a starší
- Firefox verze 5,0 a starší
- Internet Explorer verze 8-10 ve Windows 7 a starších verzích
- Internet Explorer 10 v Windows Phone 8,0
- Safari verze 6.0.4 v OS X 10.8.4 a starších verzích

## <a name="next-steps"></a>Další kroky

[Přehled protokolu TLS/SSL (Schannel SSP)](/windows-server/security/tls/tls-ssl-schannel-ssp-overview)