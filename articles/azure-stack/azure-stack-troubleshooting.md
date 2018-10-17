---
title: Řešení potíží s Microsoft Azure Stack | Dokumentace Microsoftu
description: Azure Stack řešení potíží.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: c463599190c5bfaac47a70dbca7b8a67dc830f3a
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49363471"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Řešení potíží s Microsoft Azure Stack

Tento dokument obsahuje běžné informace o odstraňování potíží pro službu Azure Stack. 

> [!NOTE]
> Vzhledem k tomu, Azure Stack Technical Development Kit (ASDK) se nabízí jako prostředí pro testování, neexistuje žádné oficiální podpora zákazníků služby podpory společnosti Microsoft. Pokud dochází k problému, nezapomeňte zaškrtnout [fórum MSDN pro Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) informace a požádejte o pomoc.  

Doporučení pro řešení potíží, které jsou popsány v této části jsou odvozeny z několika zdrojů a může nebo nemusí vyřešit konkrétní problém. Příklady kódu jsou k dispozici je a nemůže být zaručena očekávané výsledky. V této části se může časté změny a aktualizace, jak jsou implementované vylepšení produktu.

## <a name="deployment"></a>Nasazení
### <a name="deployment-failure"></a>Nasazení se nezdařilo.
Pokud během instalace dojít k selhání, můžete restartovat nasazení z kroku pomocí opětovného spuštění možnosti / skriptu nasazení.  

### <a name="at-the-end-of-asdk-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Na konci nasazení ASDK relaci Powershellu je stále otevřen a nezobrazí žádný výstup.
Toto chování je pravděpodobně právě výsledek výchozí chování příkazové okno Powershellu, pokud byla vybrána. Nasazení vývojové sady proběhlo úspěšně, ale skript se pozastavila při výběru okna. Můžete ověřit, že instalační program dokončil tím, že hledají slovo "Vyberte" v záhlaví příkazové okno.  Stisknutím klávesy ESC zrušte jeho výběr a za ním být zobrazena zpráva o dokončení.

## <a name="virtual-machines"></a>Virtuální počítače
### <a name="default-image-and-gallery-item"></a>Výchozí image a Galerie položku
Před nasazením virtuálních počítačů ve službě Azure Stack je nutné přidat položku image a Galerie Windows serveru.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Po restartování hostitele Moje Azure Stack, nemusí některé virtuální počítače spustit automaticky.
Po restartování hostitele, můžete si všimnout, že služby Azure Stack nejsou ihned k dispozici.  Důvodem je, že Azure Stack [infrastruktury virtuálních počítačů](..\azure-stack\asdk\asdk-architecture.md#virtual-machine-roles) a poskytovatelů prostředků zkontrolovat konzistenci nějakou dobu trvat, ale nakonec bude automaticky spuštěno.

Můžete si také všimnout tohoto tenanta, které virtuální počítače automaticky nespouštět po restartu hostitele služby Azure Stack development kit. To se o známý problém a vyžaduje jen několik Ruční postup jejich převést do režimu online:

1.  V hostitelském počítači Azure Stack development kit start **Správce clusteru převzetí služeb při selhání** z nabídky Start.
2.  Vyberte cluster **S Cluster.azurestack.local**.
3.  Vyberte **role**.
4.  Virtuální počítače tenanta zobrazí *Uložit* stavu. Jakmile jsou spuštěné všechny virtuální počítače infrastruktury, klikněte pravým tlačítkem na virtuální počítače klientů a vyberte **Start** obnovit virtuální počítač.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Můžu odstranily některé virtuální počítače, ale pořád ještě považuje soubory virtuálního pevného disku na disk. Toto chování se očekává se?
Ano, to je očekávané chování. Protože byla navržena tímto způsobem:

* Když odstraníte virtuální počítač, virtuální pevné disky se neodstraní. Disky jsou samostatné prostředky ve skupině prostředků.
* Pokud účet úložiště, se odstraní, odstranění je viditelné okamžitě prostřednictvím Azure Resource Manageru, ale disky, které může obsahovat stále zůstanou v úložišti dokud uvolňování paměti běží.

Pokud se zobrazí "oddělena" virtuální pevné disky, je důležité vědět, pokud jsou součástí složky pro účet úložiště, který byl odstraněn. Pokud účet úložiště nebyl odstraněn, je běžné, že jsou stále existuje.

Další informace o konfiguraci uchování prahové hodnoty a podle potřeby recyklace v [Správa účtů úložiště](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Úložiště
### <a name="storage-reclamation"></a>Recyklace úložiště
To může trvat až 14 hodin uvolňovaného kapacity se zobrazí na portálu. Recyklace místa závisí na různých faktorech včetně procento využití souborů interní kontejneru v úložišti objektů blob bloku. Proto se v závislosti na tom, kolik dat je odstraněn, neexistuje žádná záruka na množství místa, které může být získány zpět při spuštění systému uvolňování paměti.

