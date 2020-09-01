---
title: Poznámky k verzi Preview pro Azure Stack Edge | Microsoft Docs
description: Popisuje kritické otevřené problémy a řešení pro Azure Data Box Gateway, ve kterých je spuštěná verze s obecnou dostupností.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 6c29240aa3267cd93ba0c3de1f0c797ce1a1483c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084515"
---
# <a name="azure-stack-edge-with-gpu-preview-release-notes"></a>Azure Stack Edge s poznámkou pro verzi Preview GPU

Následující poznámky k verzi identifikují kritické otevřené problémy a vyřešené problémy pro 2008 verze Preview pro zařízení Azure Stack Edge s grafickým procesorem.

Poznámky k verzi se průběžně aktualizují a při zjištění kritických problémů vyžadujících alternativní řešení se přidají. Před nasazením Azure Stack hraničního zařízení pečlivě zkontrolujte informace obsažené v poznámkách k verzi.

Tato verze **Azure Stack Edge 2008** odpovídá následující verzi softwaru:

- **2.1.1328.1904**



## <a name="known-issues-in-ga-release"></a>Známé problémy ve verzi GA

Následující tabulka poskytuje souhrn známých problémů pro zařízení Azure Stack Edge.

| No. | Funkce | Problém | Alternativní řešení/komentáře |
| --- | --- | --- | --- |
| **1.** |Azure Stack Edge a Azure SQL | Vytvoření databáze SQL vyžaduje přístup správce.   |Proveďte následující kroky místo kroků 1-2 v [https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database](https://docs.microsoft.com/azure/iot-edge/tutorial-store-data-sql-server#create-the-sql-database) . <ul><li>V místním uživatelském rozhraní zařízení povolte výpočetní rozhraní. Vyberte **compute > port # > povolit pro výpočetní > použít.**</li><li>V okně PowerShellu připojeném k zařízení spusťte `Add-HcsComputeNetwork` . </li><li>Stáhnout `sqlcmd` na klientském počítači z https://docs.microsoft.com/sql/tools/sqlcmd-utility </li><li>Připojte se k IP adrese rozhraní COMPUTE (port, který byl povolený), a přidejte na konec adresy znak ", 1401".</li><li>Poslední příkaz bude vypadat takto: Sqlcmd-S {Interface IP}, 1401-U SA-P "Strong! Passw0rd".</li>Pak by se měly shodovat kroky 3-4 z aktuální dokumentace. </li></ul> |
| **2.** |Aktualizovat| Přírůstkové změny objektů BLOB obnovených prostřednictvím **aktualizace** se nepodporují. |U koncových bodů objektů BLOB jsou částečné aktualizace objektů BLOB po obnovení možné kvůli tomu, že se aktualizace nebudou nahrávat do cloudu. Například posloupnost akcí, jako například:<ul><li>Vytvoření objektu BLOB v cloudu Nebo odstraňte dříve nahraný objekt BLOB ze zařízení.</li><li>Obnovte objekt BLOB z cloudu do zařízení pomocí funkce aktualizovat.</li><li>Aktualizujte jenom část objektu BLOB pomocí rozhraní REST API sady Azure SDK.</li></ul>Tyto akce můžou vést k tomu, že aktualizované oddíly objektu BLOB se v cloudu neaktualizují. <br>**Alternativní řešení**: Nahraďte celé objekty BLOB pomocí nástrojů, jako je například Robocopy nebo běžné kopírování souborů prostřednictvím Průzkumníka nebo příkazového řádku.|
|**3.**|Throttling|Pokud se během omezování nepovolují nové zápisy do zařízení, zápisy provedené klientem systému souborů NFS selžou s chybou oprávnění odepřeno.| Tato chyba se zobrazí níže:<br>`hcsuser@ubuntu-vm:~/nfstest$ mkdir test`<br>mkdir: Nejde vytvořit adresář ' test ': oprávnění bylo odepřeno.|
|**4.**|Přijímání Blob Storage|Pokud používáte AzCopy verze 10 pro ingestování úložiště BLOB, spusťte AzCopy s následujícím argumentem: `Azcopy <other arguments> --cap-mbps 2000`| Pokud tato omezení nejsou k dispozici pro AzCopy, může potenciálně Odeslat velký počet požadavků na zařízení a způsobit problémy se službou.|
|**čl.**|Účty vrstveného úložiště|Při používání vrstvených účtů úložiště platí následující:<ul><li> Jsou podporovány pouze objekty blob bloku. Objekty blob stránky nejsou podporovány.</li><li>Neexistuje žádná podpora snímků ani kopírování API.</li><li> Ingestování úloh Hadoop přes `distcp` není podporované, protože používá operaci kopírování silným způsobem.</li></ul>||
|**6.**|Připojení ke sdílené složce NFS|Pokud kopírujete více procesů do stejné sdílené složky a `nolock` atribut se nepoužívá, mohou se během kopírování zobrazit chyby.|`nolock`Atribut musí být předán příkazu Mount ke zkopírování souborů do sdílené složky systému souborů NFS. Například: `C:\Users\aseuser mount -o anon \\10.1.1.211\mnt\vms Z:`.|
|**čl.**|Cluster Kubernetes|Při použití aktualizace na zařízení, na kterém běží cluster Kubernetes, se virtuální počítače s Kubernetes restartují a restartují. V této instanci se po aktualizaci automaticky obnoví jenom lusky nasazené se zadanými replikami.  |Pokud jste vytvořili jednotlivé lusky mimo řadič replikace bez určení sady replik, pak se tyto lusky po aktualizaci zařízení automaticky neobnoví. Tyto lusky bude nutné obnovit.<br>Sada replik nahrazuje lusky, které jsou z nějakého důvodu odstraněny nebo ukončeny, jako například selhání uzlu nebo upgrade výpadku uzlu. Z tohoto důvodu doporučujeme použít sadu replik i v případě, že vaše aplikace vyžaduje jenom jedno pod.|
|**8.**|Cluster Kubernetes|Kubernetes na Azure Stack Edge se podporuje jenom s Helm V3 nebo novějším. Další informace najdete v [nejčastějších dotazech: odebrání do pokladny](https://v3.helm.sh/docs/faq/).|
|**9.**|Azure ARC + Azure Stack Edge|Nasazení ARC Azure se nepodporuje, pokud je na zařízení Azure Stack Edge nakonfigurovaný webový proxy server.||
|**10pruhový.**|Kubernetes |Port 31000 je vyhrazený pro řídicí panel Kubernetes. Podobně platí, že ve výchozí konfiguraci jsou IP adresy 10.96.0.1 a 10.96.0.10 rezervované pro služby Kubernetes a základní službu DNS v uvedeném pořadí.|Nepoužívejte rezervované IP adresy.|
|**odst.**|Kubernetes |Kubernetes v současné době nepovoluje služby pro vyrovnávání zatížení ve více protokolech. Například služba DNS, která by musela naslouchat na TCP i UDP. |Pokud chcete toto omezení Kubernetes vyřešit pomocí MetalLB, můžete vytvořit dvě služby (jednu pro TCP, jednu pro protokol UDP) na stejném pod selektor. Tyto služby používají stejný klíč pro sdílení a specifikace. loadBalancerIP ke sdílení stejné IP adresy. Pokud máte více služeb než dostupné IP adresy, můžete sdílené IP adresy také sdílet. <br> Další informace najdete v tématu [sdílení IP adres](https://metallb.universe.tf/usage/#ip-address-sharing).|
|**12,5.**|Cluster Kubernetes|Stávající moduly Azure IoT Edge Marketplace se v clusteru Kubernetes nespustí jako hostující platforma pro IoT Edge na Azure Stack hraničním zařízení.|Moduly se musí upravit, aby se nasadily na Azure Stack hraničním zařízení. Další informace najdete v tématu Změna Azure IoT Edgech modulů z webu Marketplace na používání Azure Stack hraničního zařízení.<!-- insert link-->|
|**13,5.**|Kubernetes |Připojení vázaných na soubor nejsou u Azure IoT Edge v Kubernetes v zařízení Azure Stack Edge podporována.|IoT Edge používá vrstvu překladu k překladu `ContainerCreate` možností do Kubernetes konstrukcí. Vytváření `Binds` map do adresáře hostpath nebo vytváření a tak připojení vázaných na soubor nemůže být vázáno na cesty v kontejnerech IoT Edge.|


## <a name="next-steps"></a>Další kroky

- [Příprava na nasazení Azure Stack hraničního zařízení pomocí GPU](azure-stack-edge-gpu-deploy-prep.md)

