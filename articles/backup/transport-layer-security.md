---
title: Transport Layer Security v Azure Backup
description: Naučte se, jak povolit Azure Backup pro použití protokolu TLS (Transport Protocol Transport Layer Security), který zajistí zabezpečení dat při přenosu přes síť.
ms.topic: conceptual
ms.date: 11/01/2020
ms.openlocfilehash: ba9c9d91f562f54695a0739908c8a409d14d5852
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327113"
---
# <a name="transport-layer-security-in-azure-backup"></a>Transport Layer Security v Azure Backup

TLS (Transport Layer Security) je šifrovací protokol, který při přenosu přes síť udržuje data v bezpečí. Azure Backup využívá zabezpečení transportní vrstvy k ochraně soukromí přenášených zálohovaných dat. Tento článek popisuje kroky pro povolení protokolu TLS 1,2, který poskytuje vylepšené zabezpečení v předchozích verzích.

## <a name="earlier-versions-of-windows"></a>Starší verze Windows

Pokud počítač používá starší verze Windows, musí být nainstalované příslušné aktualizace uvedené níže a musí se použít změny registru popsané v článcích znalostní báze Knowledge Base.

|Operační systém  |Článek znalostní báze Knowledge Base |
|---------|---------|
|Windows Server 2008 SP2   |   <https://support.microsoft.com/help/4019276>      |
|Windows Server 2008 R2, Windows 7, Windows Server 2012   | <https://support.microsoft.com/help/3140245>         |

>[!NOTE]
>Aktualizace nainstaluje požadované součásti protokolu. Po dokončení instalace musíte provést změny v klíči registru uvedené v článcích znalostní báze Knowledge Base a správně povolit požadované protokoly.

## <a name="verify-windows-registry"></a>Ověření registru systému Windows

### <a name="configuring-schannel-protocols"></a>Konfigurace protokolů SChannel

Následující klíče registru zajišťují, aby byl protokol TLS 1,2 na úrovni komponenty SChannel povolen:

```reg
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "Enabled"=dword:00000001

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client]
    "DisabledByDefault"=dword:00000000
```

>[!NOTE]
>Zobrazené hodnoty jsou ve výchozím nastavení nastaveny ve Windows Serveru 2012 R2 a novějších verzích. Pokud v těchto verzích Windows chybí klíče registru, nemusíte je vytvářet.

### <a name="configuring-net-framework"></a>Konfigurace .NET Framework

Následující klíče registru konfigurují .NET Framework pro podporu silné kryptografie. Tady si můžete přečíst další informace o [konfiguraci .NET Framework](/dotnet/framework/network-programming/tls#configuring-schannel-protocols-in-the-windows-registry).

```reg
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
    "SystemDefaultTlsVersions"=dword:00000001
    "SchUseStrongCrypto" = dword:00000001
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="why-enable-tls-12"></a>Proč Povolit TLS 1,2?

TLS 1,2 je bezpečnější než u předchozích kryptografických protokolů, jako jsou SSL 2,0, SSL 3,0, TLS 1,0 a TLS 1,1. Azure Backup Services již plně podporují protokol TLS 1,2.

### <a name="what-determines-the-encryption-protocol-used"></a>Co určuje použitý šifrovací protokol?

Nejvyšší verze protokolu podporovaná klientem i serverem se vyjednává k navázání šifrované konverzace. Další informace o protokolu TLS handshake najdete v tématu [Vytvoření zabezpečené relace pomocí protokolu TLS](/windows/win32/secauthn/tls-handshake-protocol#establishing-a-secure-session-by-using-tls).

### <a name="what-is-the-impact-of-not-enabling-tls-12"></a>Jaký je dopad Nepovolit TLS 1,2?

Kvůli lepšímu zabezpečení proti útokům na downgrade protokolů se Azure Backup zahajuje zablokování verzí TLS starších než 1,2 způsobem. Toto je součást dlouhodobě posunutí napříč službami, aby nepovolovala připojení starší verze protokolů a šifrovacích sad. Služby Azure Backup a součásti plně podporují protokol TLS 1,2. Nicméně verze Windows, které neobsahují požadované aktualizace nebo některé vlastní konfigurace, můžou dál bránit nabízeným protokolům TLS 1,2. To může způsobit chyby, včetně neomezeného na jednu nebo více z následujících možností:

- Operace zálohování a obnovení můžou selhat.
- Součásti zálohování neúspěšné připojení k chybě 10054 (stávající připojení bylo vzdáleným hostitelem vynuceně ukončeno).
- Služby související s Azure Backup se neukončí ani nespustí běžným způsobem.

## <a name="additional-resources"></a>Další zdroje informací

- [Protokol TLS (Transport Layer Security)](/windows/win32/secauthn/transport-layer-security-protocol)
- [Zajištění podpory pro TLS 1,2 napříč nasazenými operačními systémy](/security/engineering/solving-tls1-problem#ensuring-support-for-tls-12-across-deployed-operating-systems)
- [Osvědčené postupy TLS (Transport Layer Security) s .NET Framework](/dotnet/framework/network-programming/tls)