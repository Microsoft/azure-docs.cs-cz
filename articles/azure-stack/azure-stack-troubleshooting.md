---
title: Řešení potíží s Microsoft Azure zásobníku | Microsoft Docs
description: Azure zásobníku řešení potíží.
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
ms.date: 02/21/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: b63fdd630647cc970a2d935619b4d3f16b8c0375
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30229886"
---
# <a name="microsoft-azure-stack-troubleshooting"></a>Řešení potíží s Microsoft Azure zásobníku

*Platí pro: Azure zásobníku Development Kit*

Tento dokument obsahuje společné informace o odstraňování potíží pro Azure zásobníku. 

Protože Azure zásobníku technické Development Kit je poskytován jako prostředí pro testování, neexistuje žádná oficiální podpora zákazníků služby podpory společnosti Microsoft. Pokud se objevil problém není zdokumentovaný, nezapomeňte zaškrtnout [fórum MSDN zásobník Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack) informace a další pomoc.  

Doporučení pro odstraňování problémů, které jsou popsané v této části jsou odvozené z několika zdrojů a může nebo nemusí vyřešit konkrétní problém. Protože je k dispozici jsou příklady kódu a nejde zaručit očekávané výsledky. V této části se může časté úpravy a aktualizace, jak jsou implementované vylepšení produktu.

## <a name="deployment"></a>Nasazení
### <a name="deployment-failure"></a>Nasazení se nezdařilo.
Pokud dojde k selhání během instalace, můžete restartovat nasazení z vadný krok pomocí-znovu spusťte možnost skriptu nasazení.  


### <a name="at-the-end-of-the-deployment-the-powershell-session-is-still-open-and-doesnt-show-any-output"></a>Na konci nasazení relaci prostředí PowerShell je stále otevřen a nezobrazí žádný výstup
Toto chování je pravděpodobně právě výsledek výchozí chování příkazové okno prostředí PowerShell, pokud byla vybrána. Nasazení development kit ve skutečnosti proběhla úspěšně, ale skript byla pozastavena při výběru okna. Můžete ověřit, že instalační program dokončil tak, že vyhledá slovo "Vyberte" v záhlaví příkazové okno.  Stisknutím klávesy ESC zrušte jeho výběr a po ní se má zobrazit zpráva dokončení.

## <a name="virtual-machines"></a>Virtuální počítače
### <a name="default-image-and-gallery-item"></a>Výchozí image a Galerie položku
Před nasazením virtuálních počítačů v zásobníku Azure je nutné přidat položku bitové kopie a galerii systému Windows Server.

### <a name="after-restarting-my-azure-stack-host-some-vms-may-not-automatically-start"></a>Po restartování počítače Moje zásobník Azure hostitele se nemusí spustit některé virtuální počítače automaticky.
Po restartování počítače hostiteli, můžete si všimnout, že nejsou ihned k dispozici služby Azure zásobníku.  Důvodem je, že Azure zásobníku [infrastrukturu virtuálních počítačů](..\azure-stack\asdk\asdk-architecture.md#virtual-machine-roles) a RPs málo chvíli trvat ke kontrole konzistence, ale server spustí automaticky.

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

Další informace o konfiguraci uchování recyklaci prahovou hodnotu a na vyžádání v [spravovat účty úložiště](azure-stack-manage-storage-accounts.md).

## <a name="storage"></a>Úložiště
### <a name="storage-reclamation"></a>Recyklace úložiště
Může trvat až 14 hodin regenerovaný kapacity objeví na portálu. Recyklace místa závisí na různých faktorech včetně procento využití interní kontejner souborů v úložišti objektů blob bloku. Proto v závislosti na tom, kolik dat je odstraněn, není zaručeno na množství místa, které může uvolnit, když běží systém uvolňování paměti.

## <a name="windows-azure-pack-connector"></a>Konektor služby Windows Azure Pack
* Pokud změníte heslo účtu azurestackadmin poté, co nasadíte zásobník Azure development kit, můžete nakonfigurovat již více cloudu režimu. Proto nebude možné se připojit k Windows Azure Pack cílovém prostředí.
* Po nastavení režimu více cloudu:
    * Uživatel může vidět řídicí panel, až po jejich Resetovat nastavení portálu. (V portálu user portal, klikněte na ikonu portálu nastavení (ozubené kolečko ikonu v pravém horním rohu). V části **obnovit výchozí nastavení**, klikněte na tlačítko **použít**.)
    * Názvy řídicího panelu se nezobrazí. Pokud k tomuto problému dochází, je třeba ručně přidat je zpět.
    * Některé dlaždice se nemusí zobrazit správně při jejich nejprve přidat na řídicí panel. Chcete-li tento problém vyřešit, aktualizujte stránku prohlížeče.



