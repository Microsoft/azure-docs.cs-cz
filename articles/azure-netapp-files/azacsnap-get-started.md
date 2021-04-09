---
title: Začínáme s nástrojem pro vytváření snímků konzistentním s aplikacemi Azure pro Azure NetApp Files | Microsoft Docs
description: Poskytuje průvodce pro instalaci nástroje Snapshot konzistentního pro aplikace Azure, který můžete použít s Azure NetApp Files.
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 25f555038c1ce0d960266eacc673a62a1ffd5ac0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98736358"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool-preview"></a>Začínáme s nástrojem pro vytváření snímků konzistentním s aplikacemi Azure (Preview)

Tento článek poskytuje průvodce pro instalaci nástroje pro vytváření konzistentních aplikací Azure, který můžete použít s Azure NetApp Files.

## <a name="getting-the-snapshot-tools"></a>Získání nástrojů snímků

Doporučuje se zákazníkům získat nejnovější verzi [instalačního programu AzAcSnap](https://aka.ms/azacsnapdownload) od Microsoftu.

Soubor pro samostatnou instalaci má přidružený [soubor s podpisem instalační služby AzAcSnap](https://aka.ms/azacsnapdownloadsignature) , který je podepsaný veřejným klíčem Microsoftu, aby bylo možné GPG ověřování staženého instalačního programu.

Po dokončení těchto stahování proveďte instalaci podle pokynů v této příručce.

### <a name="verifying-the-download"></a>Ověřuje se stahování.

Instalační program, který je ke stažení za sekundu, má přidružený soubor signatury PGP s `.asc` příponou názvu souboru. Tento soubor se dá použít k zajištění toho, aby byl stažený instalační program ověřeným souborem společnosti Microsoft. Veřejný klíč Microsoft PGP, který slouží k podepisování balíčků pro Linux, je k dispozici zde ( <https://packages.microsoft.com/keys/microsoft.asc> ) a byl použit k podepsání souboru podpisu.

Veřejný klíč Microsoft PGP se dá importovat do místního uživatele následujícím způsobem:

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

Veřejný klíč Microsoft PGP je důvěryhodný pro následující příkazy:

1. Vypíše klíče ve Storu.
2. Upravte klíč Microsoft.
3. Kontrolovat otisk prstu pomocí `fpr`
4. Podepište klíč pro jeho důvěřování.

```bash
gpg --list-keys
```

Uvedené klíče:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Výstup z interaktivní `gpg` relace podepisování veřejného klíče Microsoftu:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

Soubor podpisu PGP pro instalační program lze zkontrolovat následujícím způsobem:

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

Další informace o použití GPG najdete v tématu [Příručka ochrany osobních údajů GNU](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Podporované scénáře

Nástroje Snapshot lze použít v následujících scénářích.

- Jeden identifikátor SID
- Více identifikátorů SID
- HSR
- Škálování na více instancí
- MDC (podporuje se jenom jeden tenant)
- Jeden kontejner
- Operační systém SUSE
- Operační systém RHEL
- TYP SKLADOVÉ POLOŽKY I
- TYP SKU B

Viz [podporované scénáře pro velké instance Hana](../virtual-machines/workloads/sap/hana-supported-scenario.md)

## <a name="snapshot-support-matrix-from-sap"></a>Matice podpory snímků z SAP

Následující matice je k dispozici jako vodítko, které verze SAP HANA podporuje SAP pro zálohování snímků úložiště.

| Verze databáze       |1,0 SPS12|2,0 SPS0|2,0 SPS1|2,0 SPS2|2,0 SPS3|2,0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Jedna databáze kontejneru| √       | √      | -      | -      | -      | -      |
|MDC jeden tenant        | -       | -      | √      | √      | √      | √      |
|MDC více tenantů     | -       | -      | -      | -      | -      | √      |
> √ = <small>podporované SAP pro snímky úložiště</small>

## <a name="important-things-to-remember"></a>Důležité informace o tom, jak si pamatovat

- Po nastavení nástrojů snímků nepřetržitě Sledujte dostupné místo v úložišti a v případě potřeby odstraňte staré snímky, abyste se vyhnuli vyplňování úložiště.
- Vždy používejte nejnovější nástroje snímku.
- Používejte stejnou verzi nástrojů snímků napříč na šířku.
- Otestujte nástroje snímků a před použitím v produkčním systému si pořiďte požadované parametry a výstup příkazu.
- Při nastavování uživatele HANA pro zálohování (podrobnosti níže v tomto dokumentu) musíte nastavit uživatele pro každou instanci HANA. Vytvořte SAP HANA uživatelský účet pro přístup k instanci HANA pod SYSTEMDB (a ne v databázi SID) pro MDC. V prostředí s jedním kontejnerem se dá nastavit v rámci databáze tenanta.
- Pro přístup k úložišti musí zákazníci zadat veřejný klíč SSH. Tuto akci je třeba provést jednou pro každý uzel a pro každého uživatele, pod nímž je příkaz spuštěn.
- Počet snímků na svazek je omezený na 250.
- Pokud ručně upravíte konfigurační soubor, vždy použijte textový editor pro Linux, například "VI", nikoli editory Windows, jako je Poznámkový blok. Použití editoru Windows editor může poškodit formát souboru.
  - Nastavte `hdbuserstore` pro uživatele SAP HANA ke komunikaci s SAP HANA.
- Pro zotavení po havárii: nástroje Snapshot musí být testovány na uzlu DR před nastavením DR.
- Pravidelně Sledujte místo na disku. automatické odstranění protokolu se spravuje s `--trim` možností   `azacsnap -c backup` SAP HANA 2 a novějších verzí.
- **Riziko nepořízení snímků** – nástroje Snapshot Tools pracují pouze s uzlem SAP HANA systému zadaném v konfiguračním souboru.  Pokud tento uzel přestane být k dispozici, neexistuje žádný mechanismus pro automatické zahájení komunikace s jiným uzlem.  
  - Pro **SAP HANA Scale-Out s pohotovostním** scénářem je typická instalace a konfigurace nástrojů snímků na hlavním uzlu. Pokud ale hlavní uzel přestane být k dispozici, bude pohotovostní uzel přebírat roli hlavního uzlu. V tomto případě by měl prováděcí tým nakonfigurovat nástroje snímků na obou uzlech (hlavní a záložní), aby se předešlo nezmeškaným snímkům. V normálním stavu bude hlavní uzel přebírat snímky HANA iniciované službou crontab, ale po převzetí služeb při selhání hlavního uzlu bude nutné tyto snímky spustit z jiného uzlu, jako je nový hlavní uzel (předchozí pohotovostní). Aby bylo možné dosáhnout tohoto výsledku, musí mít pohotovostní uzel nainstalovaný nástroj snapshot Tool, povolenou komunikaci s úložištěm, nakonfigurovanou, `azacsnap.json` nakonfigurovanou a hdbuserstore příkazy crontab připravené předem.
  - V případě scénáře **SAP HANA ha HSR** se doporučuje instalovat, konfigurovat a plánovat nástroje pro snímky na obou uzlech (primární i sekundární). Pak pokud primární uzel přestane být dostupný, sekundární uzel převezme snímky pořízené na sekundárním uzlu. V normálním stavu bude primární uzel pořizovat snímky HANA iniciované nástrojem crontab a sekundární uzel by se pokusil pořizovat snímky, ale selže, protože primární funguje správně.  Ale po převzetí služeb při selhání primárního uzlu se tyto snímky spustí ze sekundárního uzlu. Aby bylo možné dosáhnout tohoto výsledku, musí mít sekundární uzel nainstalovaný nástroj snapshot Tool, povolenou komunikaci s úložištěm, `hdbuserstore` nakonfigurovanou, azacsnap.jsnakonfigurovanou a crontab předem.

## <a name="guidance-provided-in-this-document"></a>Pokyny uvedené v tomto dokumentu

K dispozici jsou následující pokyny pro ilustraci použití nástrojů Snapshot Tools.

### <a name="taking-snapshot-backups"></a>Pořizování záloh snímků

- [Jaké jsou požadavky na snímek úložiště](azacsnap-installation.md#prerequisites-for-installation)
  - [Povolit komunikaci s úložištěm](azacsnap-installation.md#enable-communication-with-storage)
  - [Povolit komunikaci s SAP HANA](azacsnap-installation.md#enable-communication-with-sap-hana)
- [Ruční pořizování snímků](azacsnap-tips.md#take-snapshots-manually)
- [Jak nastavit automatické zálohování snímků](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [Jak monitorovat snímky](azacsnap-tips.md#monitor-the-snapshots)
- [Jak odstranit snímek?](azacsnap-tips.md#delete-a-snapshot)
- [Postup obnovení snímku](azacsnap-tips.md#restore-a-snapshot)
- [Postup obnovení `boot` snímku](azacsnap-tips.md#restore-a-boot-snapshot)
- [Nejdůležitější fakta týkající se snímků](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> Snímky jsou testovány pro jeden identifikátor SID i pro více identifikátorů SID.

### <a name="performing-disaster-recovery"></a>Provádění zotavení po havárii

- [Jaké jsou požadavky na instalaci nástroje DR](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Jak nastavit zotavení po havárii](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [Jak monitorovat replikaci dat z primární na lokalitu DR](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [Jak provést převzetí služeb při selhání pro server DR?](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Další kroky

- [Nainstalovat nástroj pro vytváření snímků konzistentního vzhledem k aplikacím Azure](azacsnap-installation.md)