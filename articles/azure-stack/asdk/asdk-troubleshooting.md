---
title: "Řešení potíží s Microsoft Azure zásobníku | Microsoft Docs"
description: "Azure zásobníku Development Kit (ASDK) informace o odstraňování potíží."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 6ed3fb7c7c4de9edbf31fb2c47290e2e39ceadcd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="microsoft-azure-stack-development-kit-asdk-troubleshooting"></a>Řešení potíží s Microsoft Azure zásobníku Development Kit (ASDK)
Tento dokument obsahuje společné informace o odstraňování potíží pro ASDK. Pokud dochází k problému, který není dokumentováno, nezapomeňte zaškrtnout [fórum MSDN zásobník Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) informace a další pomoc.  

> [!IMPORTANT]
> ASDK je zkušební prostředí, a proto není žádná oficiální podporu nabízených prostřednictvím Microsoft podporu služby zákazníkům (CSS).

Doporučení pro odstraňování problémů, které jsou popsané v této části jsou odvozené z několika zdrojů a může nebo nemusí vyřešit konkrétní problém. Příklady kódu jsou poskytovány "tak, jak je" a nejde zaručit očekávané výsledky. V této části se může časté úpravy a aktualizace, jak jsou implementované vylepšení produktu.

## <a name="deployment"></a>Nasazení
### <a name="deployment-failure"></a>Nasazení se nezdařilo.
Pokud dojde k selhání během instalace, můžete restartovat nasazení z vadný krok pomocí-znovu spusťte možnost skriptu nasazení.  

### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Na konci nasazení relaci prostředí PowerShell je stále otevřen a nezobrazí žádný výstup
Toto chování je pravděpodobně právě výsledek výchozí chování příkazové okno prostředí PowerShell, pokud byla vybrána. Development kit nasazení proběhla úspěšně, ale skript byla pozastavena při výběru okna. Můžete ověřit, že instalační program dokončil tak, že vyhledá slovo "Vyberte" v záhlaví příkazové okno. Stisknutím klávesy ESC zrušte jeho výběr a po ní se má zobrazit zpráva dokončení.

## <a name="virtual-machines"></a>Virtuální počítače
### <a name="default-image-and-gallery-item"></a>Výchozí image a Galerie položku
Před nasazením virtuálních počítačů v zásobníku Azure je nutné přidat položku bitové kopie a galerii systému Windows Server.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Po restartování počítače Moje zásobník Azure hostitele se nemusí spustit některé virtuální počítače automaticky.
Po restartování počítače hostiteli, můžete si všimnout, že nejsou ihned k dispozici služby Azure zásobníku. Důvodem je, že Azure zásobníku [infrastrukturu virtuálních počítačů](asdk-architecture.md#virtual-machine-roles) a proveďte RPs některé času ke kontrole konzistence, ale server spustí automaticky.

Můžete zjistit, že klienta, které virtuální počítače se po restartu hostitele zásobník Azure development kit nespouštějte automaticky. To se o známý problém a vyžaduje jen pár Ruční postup jejich převedení do online režimu:

1.  Na hostiteli zásobník Azure development kit spustit **Správce clusteru převzetí služeb při selhání** z nabídky Start.
2.  Vyberte cluster **S Cluster.azurestack.local**.
3.  Vyberte **role**.
4.  Virtuální počítače klienta se zobrazí v *Uložit* stavu. Jakmile se všechny virtuální počítače infrastruktury jsou spuštěné, klikněte pravým tlačítkem na virtuální počítače klientů a vyberte **spustit** obnovit virtuální počítač.

### <a name="i-have-deleted-some-virtual-machines-but-still-see-the-vhd-files-on-disk-is-this-behavior-expected"></a>Odstranili některé virtuální počítače ale stále vidět soubory virtuálního pevného disku na disk. Je toto chování očekávané?
Ano, toto chování se očekává se. Protože byl navržen tímto způsobem:

* Při odstranění virtuálního počítače, virtuální pevné disky nejsou odstraněny. Disky jsou samostatné prostředky ve skupině prostředků.
* Při odstranění účtu úložiště získá odstranění je viditelná okamžitě prostřednictvím Azure Resource Manager, ale disky, které mohou obsahovat jsou uchovány stále v úložišti, dokud se nespustí uvolňování paměti.

Pokud se zobrazí "oddělena" virtuálních pevných disků, je důležité vědět, pokud jsou součástí složky pro účet úložiště, který byl odstraněn. Pokud účet úložiště nebyl odstraněn, je normální, že jsou stále existuje.

Další informace o konfiguraci uchování recyklaci prahovou hodnotu a na vyžádání v [spravovat účty úložiště](.\.\azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Úložiště
### <a name="storage-reclamation"></a>Recyklace úložiště
Může trvat až 14 hodin regenerovaný kapacity objeví na portálu. Recyklace místa závisí na různých faktorech včetně procento využití interní kontejner souborů v úložišti objektů blob bloku. Proto v závislosti na tom, kolik dat je odstraněn, není zaručeno na množství místa, které může uvolnit, když běží systém uvolňování paměti.

## <a name="next-steps"></a>Další postup
[Navštivte fórum podpory Azure zásobníku](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack)

