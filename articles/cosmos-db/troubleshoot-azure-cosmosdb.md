---
title: Správa služby Azure Cosmos DB v Průzkumníku služby Azure Storage
description: Zjistěte, jak spravovat službu Azure Cosmos DB v Průzkumníku služby Azure Storage.
Keywords: Azure Cosmos DB, Azure Storage Explorer, MongoDB
services: cosmos-db
documentationcenter: ''
author: Jejiang
manager: omafnan
editor: ''
tags: Azure Cosmos DB
ms.assetid: ''
ms.service: cosmos-db
ms.custom: Azure Cosmos DB active
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2018
ms.author: jejiang
ms.openlocfilehash: c593eb2b49d15d20a26ef735d1032d5dea45f125
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-in-storage-explorer-troubleshooting-guide-overview"></a>Průvodce řešením problémů se službou Azure Cosmos DB v Průzkumníku služby Storage – Přehled

[Azure Cosmos DB v Průzkumníku služby Storage](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer) je samostatná aplikace umožňující připojení k účtům služby Azure Cosmos DB hostovaným v Azure a suverénních cloudech z Windows, macOS nebo Linuxu. Umožňuje správu entit Azure Cosmos DB, manipulaci s daty, aktualizace uložených procedur a triggerů, stejně jako dalších entit Azure, jako jsou objekty blob a fronty služby Storage.

Tato příručka obsahuje souhrn řešení běžných problémů, ke kterým dochází ve službě Azure Cosmos DB v Průzkumníku služby Storage.

- [Problémy s přihlášením](#Sign-in-issues)
  - [Certifikát podepsaný svým držitelem v řetězu certifikátů](#Self-signed-certificate-in-certificate-chain)
  - [Nelze načíst předplatná](#Unable-to-retrieve-subscriptions)
  - [Ověřovací stránka se nezobrazuje](#Unable-to-see-the-authentication-page)
  - [Účet se nedá odebrat](#Cannot-remove-account)
- [Problém s proxy HTTP/HTTPS](#Http/Https-proxy-issue)
- [Problém s uzlem Vývoj v uzlu Místní a připojené](#Development-node-under-Local-and-Attached-node-issue)
- [Chyba připojení účtu služby Azure Cosmos DB v uzlu Místní a připojené](#Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error)
- [Chyba rozbalení uzlu služby Azure Cosmos DB](#Expand-Azure-Cosmos-DB-node-error)
- [Další kroky](#Next-steps)

<h2 id="Sign-in-issues">Problémy s přihlášením</h2>

Než budete pokračovat, zkuste svou aplikaci restartovat a podívejte se, jestli se tím problém nevyřeší.

<h2 id="Self-signed-certificate-in-certificate-chain">Certifikát podepsaný svým držitelem v řetězu certifikátů</h2>

Tato chyba se může zobrazit z několika důvodů, z nichž dva nejčastější jsou tyto:

1. Nacházíte se za transparentním proxy serverem, což znamená, že někdo (například vaše IT oddělení) zachycuje přenosy HTTP, dešifruje je a pak je šifruje pomocí certifikátu podepsaného svým držitelem.

2. Používáte software, jako je například antivirový software, který do přijímaných zpráv protokolu HTTPS vkládá certifikát SSL podepsaný svým držitelem.

Pokud Průzkumník služby Storage narazí na některý z těchto certifikátů podepsaných svým držitelem, už nemůže mít jistotu, že se s přijímanými zprávami protokolu HTTPS nemanipulovalo. Pokud však máte kopii příslušného certifikátu podepsaného svým držitelem, můžete Průzkumníku služby Storage sdělit, aby mu důvěřoval. Pokud si nejste jisti, kdo certifikát vkládá, můžete to sami zkusit zjistit provedením následujících kroků:

1. Nainstalujte OpenSSL.
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (stačí jakákoli z odlehčených verzí)
     - Mac a Linux: Mělo by být součástí operačního systému.
2. Spusťte OpenSSL.
    - Windows: Přejděte do adresáře instalace, pak do složky **/bin/** a dvakrát klikněte na soubor **openssl.exe**.
    - Mac a Linux: V terminálu spusťte příkaz **openssl**.
3. Spusťte příkaz `s_client -showcerts -connect microsoft.com:443`.
4. Vyhledejte certifikáty podepsané svým držitelem. Pokud si nejste jisti, které certifikáty jsou podepsané svým držitelem, hledejte certifikáty, jejichž předmět (s:) je stejný jako vystavitel (i:).
5.  Jakmile najdete nějaké certifikáty podepsané svým držitelem, zkopírujte u jednotlivých certifikátů veškerý text začínající na **-----BEGIN CERTIFICATE-----** a končící na **-----END CERTIFICATE-----** (včetně) a zkopírujte ho do nového souboru .cer.
6.  Otevřete Průzkumníka služby Storage a přejděte do části **Úpravy** > **Certifikáty SSL** > **Importovat certifikáty**. Pomocí nástroje pro výběr souborů vyhledejte, vyberte a otevřete soubory .cer, které jste vytvořili.

Pokud se vám podle výše uvedeného postupu nepodaří najít žádné certifikáty podepsané svým držitelem, můžete získat další nápovědu odesláním zpětné vazby.

<h2 id="Unable-to-retrieve-subscriptions">Nelze načíst předplatná</h2>

Pokud se vám po úspěšném přihlášení nedaří načíst vaše předplatná:

- Přihlaste se k webu [Azure Portal](http://portal.azure.com/) a ověřte, že má váš účet přístup k předplatným.
- Ujistěte se, že jste se přihlásili s použitím správného prostředí ([Azure](http://portal.azure.com/), [Azure (Čína)](https://portal.azure.cn/), [Azure (Německo)](https://portal.microsoftazure.de/), [Azure pro vládu USA](http://portal.azure.us/) nebo vlastní prostředí nebo Azure Stack).
- Pokud se nacházíte za proxy serverem, ujistěte se, že jste správně nakonfigurovali proxy Průzkumníka služby Storage.
- Zkuste účet odebrat a znovu přidat.
- Zkuste z domovského adresáře (např. C:\Users\ContosoUser) odstranit následující soubory a pak znovu přidat účet:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Při přihlašování sledujte případné chybové zprávy v konzole vývojářských nástrojů (F12).

![konzola](./media/troubleshoot-cosmosdb/console.png)

<h2 id="Unable-to-see-the-authentication-page">Ověřovací stránka se nezobrazuje</h2>  

Pokud se vám nezobrazuje ověřovací stránka:

- V závislosti na rychlosti vašeho připojení může načtení přihlašovací stránky nějakou dobu trvat. Počkejte alespoň minutu, než dialogové okno ověřování zavřete.
- Pokud se nacházíte za proxy serverem, ujistěte se, že jste správně nakonfigurovali proxy Průzkumníka služby Storage.
- Stisknutím klávesy F12 otevřete konzolu pro vývojáře. V konzole pro vývojáře sledujte odpovědi a podívejte se, jestli nenajdete nějaké vodítko k tomu, proč ověřování nefunguje.

<h2 id="Cannot-remove-account">Účet se nedá odebrat</h2>

Pokud nemůžete odebrat účet nebo pokud odkaz na opětovné ověření nic nedělá:

- Zkuste z domovského adresáře odstranit následující soubory a pak znovu přidat účet:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Pokud chcete odebrat prostředky služby Storage připojené sdíleným přístupovým podpisem, odstraňte:
  - Složku %AppData%/StorageExplorer ve Windows.
  - Složku /Users/<vaše_jméno>/Library/Applicaiton Support/StorageExplorer v systému Mac.
  - Složku ~/.config/StorageExplorer v Linuxu.
  - Pokud tyto soubory odstraníte, **budete muset zadat všechny své přihlašovací údaje znovu**.


<h2 id="Http/Https-proxy-issue">Problém s proxy HTTP/HTTPS</h2>

Při konfiguraci proxy HTTP/HTTPS v Průzkumníku služby Azure Storage nemůžete v levém stromu vypsat uzly služby Azure Cosmos DB. Jedná se o známý problém, který bude opravený v další vydané verzi. Prozatím můžete jako alternativní řešení použít Průzkumníka dat služby Azure Cosmos DB na webu Azure Portal. 

<h2 id="Development-node-under-Local-and-Attached-node-issue">Problém s uzlem Vývoj v uzlu Místní a připojené</h2>

Po kliknutí na uzel Vývoj v uzlu Místní a připojené v levém stromu nedojde k žádné reakci.  Jedná se o očekávané chování. Místní emulátor služby Azure Cosmos DB bude podporovaný v další vydané verzi.

![Uzel Vývoj](./media/troubleshoot-cosmosdb/development.png)

<h2 id="Attaching-Azure-Cosmos-DB-account-in-Local-and-Attached-node-error">Chyba připojení účtu služby Azure Cosmos DB v uzlu Místní a připojené</h2>

Pokud se po připojení účtu služby Azure Cosmos DB v uzlu Místní a připojené zobrazí následující chyba, zkontrolujte, jestli používáte správný připojovací řetězec.

![Chyba připojení účtu služby Azure Cosmos DB v uzlu Místní a připojené](./media/troubleshoot-cosmosdb/attached-error.png)

<h2 id="Expand-Azure-Cosmos-DB-node-error">Chyba rozbalení uzlu služby Azure Cosmos DB</h2>

Při pokusu o rozbalení uzlů v levém stromu se může zobrazit následující chyba. 

![Chyba rozbalení](./media/troubleshoot-cosmosdb/expand-error.png)

Vyzkoušejte následující návrhy:

- Zkontrolujte, jestli neprobíhá zřizování účtu služby Azure Cosmos DB, a zkuste to znovu po úspěšném vytvoření účtu.
- Pokud je účet v uzlu Rychlý přístup nebo Místní a připojené, zkontrolujte, jestli se účet neodstranil. Pokud ano, musíte uzel odebrat ručně.

<h2 id="Next-steps">Další kroky</h2>

Pokud vám žádné z předchozích řešení nepomohlo, požádejte o pomoc s řešením problému odesláním e-mailu s podrobnostmi o problému týmu vývojářských nástrojů pro službu Azure Cosmos DB ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)).





