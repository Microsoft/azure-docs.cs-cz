---
title: Nasazení pureScale IBM DB2 v Azure
description: Zjistěte, jak nasadit architekturu příklad nepoužil migrace organizace ze své prostředí IBM DB2 používají z/OS a pureScale IBM DB2 v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 104730d94134d935f56fb95fd55d05b515e9f501
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54245561"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Nasazení pureScale IBM DB2 v Azure

Tento článek popisuje, jak nasadit [příklad architektury](ibm-db2-purescale-azure.md) , který podnikový zákazník naposledy použité k migraci z databáze IBM DB2 prostředí používají z/OS a pureScale IBM DB2 v Azure.

Postupujte podle kroků pro migraci, najdete v tématu Instalace skripty v [DB2onAzure](http://aka.ms/db2onazure) úložišti na Githubu. Tyto skripty jsou založené na architektuře pro úlohy zpracování (OLTP) typické, online transaction.

## <a name="get-started"></a>Začínáme

Tuto architekturu nasadit, stáhněte a spusťte skript deploy.sh součástí [DB2onAzure](http://aka.ms/db2onazure) úložišti na Githubu.

Úložiště také obsahuje skripty pro nastavení řídicím panelem Grafana. Řídicí panel můžete použít k dotazování Prometheus, open source monitorování a systém výstrah, které jsou součástí DB2.

> [!NOTE]
> Deploy.sh skript na straně klienta vytvoří privátního klíče SSH a předává je do šablony nasazení přes protokol HTTPS. Pro lepší zabezpečení, doporučujeme použít [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) ukládat tajné klíče, klíče a hesla.

## <a name="how-the-deployment-script-works"></a>Jak funguje skriptu nasazení

Deploy.sh skript vytvoří a nakonfiguruje prostředky Azure pro tuto architekturu. Skript vás vyzve k zadání předplatného Azure a virtuální počítače použité v cílovém prostředí a poté provede následující operace:

-   Vytvoří skupinu prostředků, virtuální sítě a podsítí v Azure pro instalaci

-   Nastaví skupiny zabezpečení sítě a SSH pro prostředí

-   Nastaví síťové karty na GlusterFS i DB2 pureScale virtuálních počítačů

-   Vytvoří virtuální počítače s úložištěm GlusterFS

-   Vytvoří virtuální počítač jumpbox

-   Vytvoří virtuální počítače pureScale DB2

-   Vytvoří virtuální počítač s kopií clusteru této příkazy ping pro zjištění pureScale DB2

-   Vytvoří virtuální počítač s Windows a použít pro testování, ale nic neinstaluje na něm

V dalším kroku skripty nasazení nastavení iSCSI virtuální síť SAN (vSAN) pro sdílené úložiště v Azure. V tomto příkladu iSCSI připojí k GlusterFS. Toto řešení také nabízí možnost instalace cíle iSCSI jako jeden uzel Windows. iSCSI poskytuje rozhraní sdílené blokové úložiště přes TCP/IP, které umožňuje postup DB2 pureScale nastavení použít rozhraní zařízení pro připojení ke sdílenému úložišti. Základy GlusterFS, najdete v článku [architektury: Typy svazků](https://docs.gluster.org/en/latest/Quick-Start-Guide/Architecture/) téma v Gluster dokumentace.

Skripty nasazení spusťte tyto obecné kroky:

1.  Pomocí GlusterFS nastavíte cluster sdíleného úložiště v Azure. Tento krok zahrnuje alespoň dva uzly s Linuxem. Podrobnosti instalace najdete v tématu [nastavení Red Hat Gluster Storage ve službě Microsoft Azure](https://access.redhat.com/documentation/en-us/red_hat_gluster_storage/3.1/html/deployment_guide_for_public_cloud/chap-documentation-deployment_guide_for_public_cloud-azure-setting_up_rhgs_azure) v Red Hat Gluster dokumentaci.

2.  Nastavení rozhraní iSCSI s přímým přístupem na cílové servery s Linuxem pro GlusterFS. Podrobnosti instalace najdete v tématu [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) v příručce pro správu GlusterFS.

3.  Nastavte iniciátor iSCSI na virtuální počítače s Linuxem. Iniciátor přístup k clusteru GlusterFS pomocí cíle iSCSI. Podrobnosti instalace najdete v tématu [konfigurace s iSCSI cíl a iniciátor v systému Linux](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) v dokumentaci k RootUsers.

4.  Nainstalujte GlusterFS jako vrstvu úložiště pro rozhraní iSCSI.

Po vytvoření skriptů zařízení iSCSI, posledním krokem je instalace DB2 pureScale. Jako součást instalace pureScale DB2 [IBM spektra škálování](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (dříve označované jako GPFS) kompiluje a nainstalovat na clusteru GlusterFS. Tato clusterového souborového systému umožňuje pureScale DB2 ke sdílení dat mezi virtuálními počítači, na kterých běží modul pureScale DB2. Další informace najdete v tématu [IBM spektra škálování](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) dokumentaci na webu IBM.

## <a name="db2-purescale-response-file"></a>Soubor odpovědí pureScale DB2

Úložiště GitHub zahrnuje DB2server.rsp, soubor odpovědí (.rsp), který umožňuje generovat automatizovaný skript pro instalaci pureScale DB2. Následující tabulka uvádí možnosti pureScale DB2, které používá soubor odpovědí pro instalaci. Soubor odpovědí můžete přizpůsobit podle potřeby pro vaše prostředí.

> [!NOTE]
> Je součástí ukázkového souboru odpovědí DB2server.rsp, [DB2onAzure](http://aka.ms/db2onazure) úložišti na Githubu. Pokud používáte tento soubor, musíte upravit předtím, než můžete pracovat ve vašem prostředí.

| Název obrazovky               | Pole                                        | Hodnota                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Uvítání                   |                                              | Nová instalace                                                                                           |
| Vyberte produkt          |                                              | DB2 verze 11.1.3.3. Edice serveru s DB2 pureScale                                              |
| Konfigurace             | Adresář                                    | /Data1/OPT/IBM/DB2/V11.1                                                                              |
|                           | Vybrat typ instalace                 | Typické                                                                                               |
|                           | Souhlasím s podmínkami IBM                     | Zaškrtnuté                                                                                               |
| Vlastníka instance            | Existující Instance pro uživatele, uživatelského jména        | DB2sdin1                                                                                              |
| Ohraničených uživatele               | Existující uživatele, uživatelského jména                     | DB2sdfe1                                                                                              |
| Systém souborů clusteru       | Sdílené cesty k disku oddílu zařízení            | /dev/DM-2                                                                                             |
|                           | Bod připojení                                  | / DB2sd\_1804a                                                                                         |
|                           | Sdíleném disku pro data                         | /dev/DM-1                                                                                             |
|                           | Přípojný bod (Data)                           | / DB2fs/datafs1                                                                                        |
|                           | Sdílený disk protokolu                          | /dev/DM-0                                                                                             |
|                           | Přípojný bod (protokolu)                            | / DB2fs/logfs1                                                                                         |
|                           | DB2 Rozhodujícího prvku clusteru služby. Cesta k zařízení | /dev/DM-3                                                                                             |
| Seznam hostitelů                 | D1 [eth1] d2 [eth1] cf1 [eth1] F2 [eth1] |                                                                                                       |
|                           | Upřednostňované primární CF                         | cf1                                                                                                   |
|                           | Upřednostňované sekundární CF                       | F2                                                                                                   |
| Soubor odpovědí a souhrn | první možnost                                 | Nainstalujte DB2 Server Edition s funkcí pureScale IBM DB2 a uložit Moje nastavení v souboru odpovědí. |
|                           | Název souboru odpovědí                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Poznámky k nasazení

- Po restartování počítače na virtuálním počítači, kde probíhá nastavení můžete změnit hodnoty /dev-dm0, /dev-dm1, /dev-dm2 a /dev-dm3 (d0 v automatického skriptu). Pokud chcete najít správné hodnoty, můžete vydejte následující příkaz před dokončením soubor odpovědí na serveru, ve kterém se spustí instalační program:

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

- Skripty pro nastavení pomocí aliasů pro disky iSCSI tak, aby názvy lze snadno nalézt.

- Při spuštění instalačního skriptu d0, **/dev/dm -\***  hodnoty můžou být různé d1, cf0 a cf1. Rozdíl v hodnotách nemá vliv na nastavení pureScale DB2.

## <a name="troubleshooting-and-known-issues"></a>Řešení potíží a známé problémy

Úložiště GitHub se vzorovými zahrnuje znalostní báze, který autoři udržovat. Vypíše potenciální problémy, může být a jejich řešení, můžete to zkusit. Například známé problémy může dojít, když:

-   Se snažíte dosáhnout IP adresu brány.

-   Při kompilaci General Public License (GPL).

-   Bezpečnostní ověření typu handshake mezi hostiteli se nezdaří.

-   DB2 instalační program zjistí existující systém souborů.

-   Ručně instalujete IBM spektra škálování.

-   Když už je vytvořená IBM spektra škálování instalujete DB2 pureScale.

-   Odstraňujete DB2 pureScale a IBM spektra škálování.

Další informace o těchto a dalších známých problémů naleznete v souboru kb.md v [DB2onAzure](http://aka.ms/DB2onAzure) úložiště.

## <a name="next-steps"></a>Další postup

-   [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)

-   [Vytváří se požadovaní uživatelé pro DB2 pureScale instalace funkce](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - vytvořit instanci příkaz](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [DB2 pureScale clustery Data řešení](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Modernizace Alliance platformy: IBM DB2 v Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)

-   [Azure virtuální datové centrum Lift and Shift Průvodce](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
