---
title: Nasazení IBM DB2 pureScale v Azure
description: Zjistěte, jak nasadit ukázkovou architekturu, která se nedávno používala k migraci podniku z prostředí IBM DB2 běžícího na z/OS na IBM DB2 pureScale v Azure.
author: njray
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: 98e912894a4d93a057a2f6a2153d0690deaed250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968898"
---
# <a name="deploy-ibm-db2-purescale-on-azure"></a>Nasazení IBM DB2 pureScale v Azure

Tento článek popisuje, jak nasadit [ukázkovou architekturu,](ibm-db2-purescale-azure.md) kterou podnikový zákazník nedávno použil k migraci ze svého prostředí IBM DB2 spuštěného na z/OS na IBM DB2 pureScale v Azure.

Postup ujte podle kroků použitých pro migraci, podívejte se na instalační skripty v úložišti [DB2onAzure](https://aka.ms/db2onazure) na GitHubu. Tyto skripty jsou založeny na architektuře pro typické, středně velké online zpracování transakcí (OLTP) zatížení.

## <a name="get-started"></a>Začínáme

Chcete-li tuto architekturu nasadit, stáhněte a spusťte skript deploy.sh nalezený v úložišti [DB2onAzure](https://aka.ms/db2onazure) na GitHubu.

Úložiště má také skripty pro nastavení řídicího panelu Grafana. Řídicí panel můžete použít k dotazování Společnosti Prometheus, systému monitorování a upozorňování s otevřeným zdrojovým kódem, který je součástí databáze DB2.

> [!NOTE]
> Skript deploy.sh na straně klienta vytvoří privátní klíče SSH a předá je šabloně nasazení přes protokol HTTPS. Pro větší zabezpečení doporučujeme k ukládání tajných kódů, klíčů a hesel pomocí [služby Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

## <a name="how-the-deployment-script-works"></a>Jak funguje skript nasazení

Skript deploy.sh vytvoří a nakonfiguruje prostředky Azure pro tuto architekturu. Skript vás vyzve k odběru Azure a virtuálním počítačům používaným v cílovém prostředí a pak provede následující operace:

-   Nastaví skupinu prostředků, virtuální síť a podsítě v Azure pro instalaci.

-   Nastaví skupiny zabezpečení sítě a SSH pro prostředí.

-   Nastaví více nic na sdílené úložiště a DB2 pureScale virtuální počítače.

-   Vytvoří virtuální počítače sdíleného úložiště. Pokud používáte Storage Spaces Direct nebo jiné řešení úložiště, přečtěte si informace [o tom, že je přehled prostorů úložiště](/windows-server/storage/storage-spaces/storage-spaces-direct-overview).

-   Vytvoří virtuální počítač jumpbox.

-   Vytvoří virtuální počítače DB2 pureScale.

-   Vytvoří virtuální počítač svědka, který DB2 pureScale ping. Přeskočit tuto část nasazení, pokud vaše verze Db2 pureScale nevyžaduje důkaz čehož připojili své.

-   Vytvoří virtuální počítač s Windows, který se použije k testování, ale nic na něj nenainstaluje.

Skripty nasazení dále nastavují síť pro oblast virtuálního úložiště iSCSI (vSAN) pro sdílené úložiště v Azure. V tomto příkladu se iSCSI připojuje ke sdílenému clusteru úložiště. V původním zákaznickém řešení byl použit GlusterFS. IBM však tento přístup již nepodporuje. Chcete-li zachovat podporu ibm, musíte použít podporovaný systém souborů kompatibilní s iSCSI. Microsoft nabízí Storage Spaces Direct (S2D) jako volitelné příslušenství.

Toto řešení také umožňuje nainstalovat cíle iSCSI jako jeden uzel systému Windows. iSCSI poskytuje rozhraní úložiště sdílených bloků přes protokol TCP/IP, které umožňuje použití rozhraní zařízení pro připojení ke sdílenému úložišti pomocí rozhraní zařízení.

Skripty nasazení spouštějí tyto obecné kroky:

1.  Nastavte cluster sdíleného úložiště v Azure. Tento krok zahrnuje alespoň dva uzly Linuxu.

2.  Nastavte rozhraní iSCSI Direct na cílových serverech Linux pro cluster sdíleného úložiště.

3.  Nastavte iniciátor iSCSI na virtuálních počítačích S IGSI. Iniciátor bude přistupovat ke clusteru sdíleného úložiště pomocí cíle iSCSI. Podrobnosti o nastavení najdete v [tématu Konfigurace cíle iSCSI a iniciátoru v Linuxu](https://www.rootusers.com/how-to-configure-an-iscsi-target-and-initiator-in-linux/) v dokumentaci RootUsers.

4.  Nainstalujte vrstvu sdíleného úložiště pro rozhraní iSCSI.

Po skripty vytvořit zařízení iSCSI, posledním krokem je instalace DB2 pureScale. V rámci nastavení DB2 pureScale je [IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0057167.html) (dříve známá jako GPFS) kompilována a instalována v clusteru GlusterFS. Tento clusterovaný souborový systém umožňuje DB2 pureScale sdílet data mezi virtuálními počítači, které spouštějí modul DB2 pureScale. Další informace naleznete v dokumentaci [k produktu IBM Spectrum Scale](https://www.ibm.com/support/knowledgecenter/en/STXKQY_4.2.0/ibmspectrumscale42_welcome.html) na webových stránkách IBM.

## <a name="db2-purescale-response-file"></a>SOUBOR odpovědí DB2 pureScale

Úložiště GitHub obsahuje SOUBOR DB2server.rsp, soubor odpovědi (.rsp), který umožňuje generovat automatizovaný skript pro instalaci DB2 pureScale. V následující tabulce jsou uvedeny možnosti DB2 pureScale, které soubor odpovědí používá pro nastavení. Soubor odpovědí můžete přizpůsobit podle potřeby pro vaše prostředí.

> [!NOTE]
> Ukázkový soubor odpovědi DB2server.rsp je součástí úložiště [DB2onAzure](https://aka.ms/db2onazure) na GitHubu. Pokud použijete tento soubor, je nutné jej upravit, aby mohl fungovat ve vašem prostředí.

| Název obrazovky               | Pole                                        | Hodnota                                                                                                 |
|---------------------------|----------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Vítáme vás                   |                                              | Nová instalace                                                                                           |
| Výběr produktu          |                                              | DB2 verze 11.1.3.3. Serverové edice s DB2 pureScale                                              |
| Konfigurace             | Adresář                                    | /data1/opt/ibm/db2/V11.1                                                                              |
|                           | Výběr typu instalace                 | Typické                                                                                               |
|                           | Souhlasím s podmínkami IBM                     | Zaškrtnuté                                                                                               |
| Vlastník instance            | Existující uživatel pro instanci, uživatelské jméno        | DB2sdin1                                                                                              |
| Oplocený uživatel               | Existující uživatel, uživatelské jméno                     | DB2sdfe1                                                                                              |
| Systém souborů clusteru       | Cesta k zařízení oddílu sdíleného disku            | /vývoj/dm-2                                                                                             |
|                           | Přípojkový bod                                  | /DB2sd\_1804a                                                                                         |
|                           | Sdílený disk pro data                         | /vývoj/dm-1                                                                                             |
|                           | Přípojkový bod (data)                           | /DB2fs/datafs1                                                                                        |
|                           | Sdílený disk pro protokol                          | /vývoj/dm-0                                                                                             |
|                           | Přípojkový bod (protokol)                            | /DB2fs/logfs1                                                                                         |
|                           | Technologie Clusterové služby DB2. Cesta k zařízení | /vývoj/dm-3                                                                                             |
| Seznam hostitelů                 | d1 [eth1], d2 [eth1], cf1 [eth1], cf2 [eth1] |                                                                                                       |
|                           | Preferované primární CF                         | cf1                                                                                                   |
|                           | Preferovaný sekundární CF                       | cf2                                                                                                   |
| Soubor odpovědí a souhrn | první možnost                                 | Instalace db2 Server Edition s funkcí IBM DB2 pureScale a uložení nastavení do souboru odpovědí |
|                           | Název souboru odpovědi                           | /root/DB2server.rsp                                                                                   |

### <a name="notes-about-this-deployment"></a>Poznámky k tomuto nasazení

- Hodnoty pro /dev-dm0, /dev-dm1, /dev-dm2 a /dev-dm3 se mohou změnit po restartování virtuálního počítače, kde probíhá instalace (d0 v automatickém skriptu). Chcete-li najít správné hodnoty, můžete před dokončením souboru odpovědí na serveru, na kterém bude instalace spuštěna, vydat následující příkaz:

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

- Instalační skripty používají pro disky iSCSI aliasy, aby bylo možné snadno najít skutečné názvy.

- Při spuštění instalačního skriptu na d0 hodnoty **/dev/dm-\* ** se mohou lišit na d1, cf0 a cf1. Rozdíl v hodnotách nemá vliv na nastavení DB2 pureScale.

## <a name="troubleshooting-and-known-issues"></a>Řešení potíží a známé problémy

Úložiště GitHub obsahuje znalostní bázi, kterou autoři udržují. Obsahuje seznam potenciálních problémů, které můžete mít, a řešení, která můžete vyzkoušet. Známé problémy mohou například nastat, když:

-   Pokoušíte se dosáhnout IP adresy brány.

-   Sestavujete general public license (GPL).

-   Zabezpečení handshake mezi hostiteli nezdaří.

-   Instalační program DB2 detekuje existující systém souborů.

-   Ručně instalujete IBM Spectrum Scale.

-   Když je IBM Spectrum Scale již vytvořena, instalujete DB2 pureScale.

-   Odstraňujete DB2 pureScale a IBM Spectrum Scale.

Další informace o těchto a dalších známých problémech naleznete v souboru kb.md v úložiště [DB2onAzure.](https://aka.ms/DB2onAzure)

## <a name="next-steps"></a>Další kroky

-   [Vytvoření požadovaných uživatelů pro instalaci funkce DB2 pureScale](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)

-   [DB2icrt - příkaz Vytvořit instanci](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)

-   [Datové řešení clusterů PURE2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)

-   [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

-   [Průvodce výtahem a směnami virtuálního datového centra Azure](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
