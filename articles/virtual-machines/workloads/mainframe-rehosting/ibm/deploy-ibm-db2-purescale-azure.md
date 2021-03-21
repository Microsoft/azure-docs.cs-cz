---
title: Nasazení IBM DB2 pureScale v Azure
description: Naučte se, jak nasadit ukázkovou architekturu, která se nedávno použila k migraci podniku z prostředí IBM DB2 běžícího na z/OS do IBM DB2 pureScale v Azure.
author: njray
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 481816ca0c1fecfee9396f6cc40582695b0952f2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550191"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Nasazení IBM DB2 pureScale v Azure

Tento článek popisuje, jak nasadit [ukázkovou architekturu](ibm-db2-purescale-azure.md) , kterou Podnikoví zákazníci nedávno použili k migraci z prostředí IBM DB2 běžícímu na z/OS do IBM DB2 PureScale v Azure.

Pokud chcete postupovat podle kroků používaných pro migraci, přečtěte si instalační skripty v úložišti [DB2onAzure](https://aka.ms/db2onazure) na GitHubu. Tyto skripty jsou založené na architektuře pro typickou úlohu zpracování online zpracování transakcí (OLTP).

## <a name="get-started"></a>Začínáme

Pokud chcete nasadit tuto architekturu, Stáhněte a spusťte skript deploy.sh, který najdete v úložišti [DB2onAzure](https://aka.ms/db2onazure) na GitHubu.

Úložiště má také skripty pro nastavení řídicího panelu Grafana. Řídicí panel můžete použít k dotazování na Prometheus, open source monitorování a systém výstrah, který je součástí DB2.

> [!NOTE]
> Skript deploy.sh na klientovi vytvoří privátní klíče SSH a předá je do šablony nasazení přes protokol HTTPS. Pro zajištění vyššího zabezpečení doporučujeme použít [Azure Key Vault](../../../../key-vault/general/overview.md) k ukládání tajných klíčů, klíčů a hesel.

## <a name="how-the-deployment-script-works"></a>Jak skript nasazení funguje

Skript deploy.sh vytvoří a nakonfiguruje prostředky Azure pro tuto architekturu. Skript vás vyzve k zadání předplatného Azure a virtuálních počítačů používaných v cílovém prostředí a pak provede následující operace:

-   Nastaví pro instalaci skupinu prostředků, virtuální síť a podsítě v Azure.

-   Nastaví skupiny zabezpečení sítě a SSH pro prostředí.

-   Nastaví více síťových adaptérů na sdíleném i virtuálním počítači DB2 pureScale.

-   Vytvoří virtuální počítače se sdíleným úložištěm. Pokud používáte Prostory úložiště s přímým přístupem nebo jiné řešení úložiště, přečtěte si téma [přehled prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

-   Vytvoří virtuální počítač s JumpBox.

-   Vytvoří virtuální počítače DB2 pureScale.

-   Vytvoří virtuální počítač s kopií clusteru, který pureScale otestuje. Tuto část nasazení přeskočte, pokud vaše verze Db2 pureScale nevyžaduje určující disk.

-   Vytvoří virtuální počítač s Windows, který se má použít pro testování, ale neinstaluje na něj cokoli.

V dalším kroku se skripty nasazení nastavily jako virtuální síť SAN (síti vSAN) iSCSI pro sdílené úložiště v Azure. V tomto příkladu se iSCSI připojí ke clusteru sdíleného úložiště. V původním zákaznickém řešení se použilo GlusterFS. Společnost IBM už ale tento přístup nepodporuje. Aby bylo možné zachovat podporu od společnosti IBM, je nutné použít podporovaný systém souborů kompatibilní s technologií iSCSI. Microsoft nabízí Prostory úložiště s přímým přístupem (S2D) jako možnost.

Toto řešení také nabízí možnost nainstalovat cíle iSCSI jako jeden uzel systému Windows. iSCSI poskytuje rozhraní sdíleného blokového úložiště přes protokol TCP/IP, které umožňuje, aby instalační program DB2 pureScale k připojení ke sdílenému úložišti používal rozhraní zařízení.

Skripty nasazení spouštějí tyto obecné kroky:

1.  Nastavte cluster sdíleného úložiště v Azure. Tento krok zahrnuje aspoň dva uzly Linux.

2.  Nastavte rozhraní iSCSI Direct na cílových serverech Linux pro sdílený cluster úložiště.

3.  Nastavte iniciátor iSCSI na virtuálních počítačích se systémem Linux. Iniciátor bude přistupovat ke clusteru sdíleného úložiště pomocí cíle iSCSI. Podrobné informace o instalaci najdete v tématu [Postup konfigurace cíle iSCSI a iniciátoru v systému Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) v dokumentaci k RootUsers.

4.  Nainstalujte sdílenou vrstvu úložiště pro rozhraní iSCSI.

Až skripty vytvoří zařízení iSCSI, posledním krokem je instalace DB2 pureScale. V rámci nastavení DB2 pureScale se v clusteru GlusterFS zkompiluje a nainstaluje [IBM spektrum](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (dřív označované jako GPFS). Tento clusterový systém souborů umožňuje DB2 pureScale sdílet data mezi virtuálními počítači, na kterých běží modul pureScale DB2. Další informace najdete v dokumentaci k [IBM spektra](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) na webu IBM.

## <a name="db2-purescale-response-file"></a>Soubor odpovědi DB2 pureScale

Úložiště GitHub obsahuje soubor DB2server. rsp, odpověď (. rsp), který umožňuje vygenerovat automatizovaný skript pro instalaci DB2 pureScale. V následující tabulce jsou uvedené možnosti DB2 pureScale, které soubor odpovědí používá pro instalaci. Soubor odpovědí můžete přizpůsobit podle potřeby pro vaše prostředí.

> [!NOTE]
> Do úložiště [DB2onAzure](https://aka.ms/db2onazure) na GitHubu je zahrnut ukázkový soubor odezvy DB2server. rsp. Pokud tento soubor použijete, musíte ho před tím, než bude fungovat ve vašem prostředí, upravit.

| Název obrazovky               | Pole                                        | Hodnota                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Vítáme vás                   |                                              | Nová instalace                                                                                           |
| Zvolit produkt          |                                              | 11.1.3.3 verze DB2 Edice serveru s DB2 pureScale                                              |
| Konfigurace             | Adresář                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Vyberte typ instalace.                 | Typické                                                                                               |
|                           | Souhlasím s podmínkami IBM                     | Zaškrtnuto                                                                                               |
| Vlastník instance            | Stávající uživatel instance, uživatelské jméno        | DB2sdin1                                                                                              |
| Uživatel s ochranou               | Existující uživatel, uživatelské jméno                     | DB2sdfe1                                                                                              |
| Systém souborů clusteru       | Cesta k zařízení oddílu sdíleného disku            | /dev/dm-2                                                                                             |
|                           | Přípojný bod                                  | /DB2sd \_ 1804a                                                                                         |
|                           | Sdílený disk pro data                         | /dev/dm-1                                                                                             |
|                           | Přípojný bod (data)                           | /DB2fs/datafs1                                                                                        |
|                           | Sdílený disk pro protokol                          | /dev/dm-0                                                                                             |
|                           | Přípojný bod (protokol)                            | /DB2fs/logfs1                                                                                         |
|                           | Clusterové služby DB2 rozhodujícího prvku. Cesta k zařízení | /dev/dm-3                                                                                             |
| Seznam hostitelů                 | D1 [eth1], D2 [eth1], CF1 [eth1], CF2 [eth1] |                                                                                                       |
|                           | Preferovaný primární CF                         | CF1                                                                                                   |
|                           | Preferovaný sekundární CF                       | cf2                                                                                                   |
| Soubor odpovědi a souhrn | první možnost                                 | Nainstalujte DB2 Server Edition pomocí funkce IBM DB2 pureScale a uložte nastavení do souboru odpovědí. |
|                           | Název souboru odpovědi                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Poznámky k tomuto nasazení

- Hodnoty pro/dev-dm0,/dev-DM1,/dev-dm2 a/dev-dm3 se můžou po restartování na virtuálním počítači, kde se má nastavení uskutečnit (D0 do automatizovaného skriptu), změnit. Chcete-li najít správné hodnoty, můžete před dokončením souboru odpovědí na serveru, kde bude instalační program běžet, vydat následující příkaz:

   ```
   [root\@d0 rhel]\# ls -als /dev/mapper
   total 0
   0 drwxr-xr-x 2 root root 140 May 30 11:07 .
   0 drwxr-xr-x 19 root root 4060 May 30 11:31 ..
   0 crw------- 1 root root 10, 236 May 30 11:04 control
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2data1 -\> ../dm-1
   0 lrwxrwxrwx 1 root root 7 May 30 11:07 db2log1 -\> ../dm-0
   0 lrwxrwxrwx 1 root root 7 May 30 11:26 db2shared -\> ../dm-2
   0 lrwxrwxrwx 1 root root 7 May 30 11:08 db2tieb -\> ../dm-3
   ```

- Skripty pro instalaci používají aliasy pro disky iSCSI, aby se mohly snadno najít skutečné názvy.

- Pokud je instalační skript spuštěn v D0, hodnoty **/dev/DM- \*** se mohou lišit v sestavách D1, cf0 a CF1. Rozdíl v hodnotách nemá vliv na nastavení pureScale DB2.

## <a name="troubleshooting-and-known-issues"></a>Řešení potíží a známé problémy

Úložiště GitHub zahrnuje znalostní bázi, kterou autoři udržují. Uvádí možné problémy, které byste mohli mít, a řešení, která můžete vyzkoušet. Například známé problémy mohou nastat v těchto případech:

-   Pokoušíte se připojit k IP adrese brány.

-   Kompilujete si obecnou veřejnou licenci (GPL).

-   Bezpečnostní signalizace mezi hostiteli se nezdařila.

-   Instalační program DB2 detekuje existující systém souborů.

-   Ruční instalace škály IBM pro spektrum

-   Instalujete DB2 pureScale v době, kdy už je vytvořeno IBM spektrum.

-   Odebíráte si škálu DB2 pureScale a IBM pro spektrum.

Další informace o těchto a dalších známých problémech najdete v souboru kb.md v úložišti [DB2onAzure](https://aka.ms/DB2onAzure) .

## <a name="next-steps"></a>Další kroky

-   [Vytváření požadovaných uživatelů pro instalaci funkce DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt – vytvoření instance – příkaz](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Řešení pro data o clusterech DB2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Průvodce zvednutím a posunutím virtuálního datového centra Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
