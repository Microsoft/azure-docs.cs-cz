---
title: Použití vlastních certifikátů se zařízeními Azure Data Box/Azure Data Box Heavy
description: Jak používat vlastní certifikáty pro přístup k místnímu webovému uživatelskému rozhraní a úložišti blogu na Data Box nebo Data Box Heavym zařízení.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 12/08/2020
ms.author: alkohli
ms.openlocfilehash: dab34b26d8237d743e22149ed0da2dd9471d7431
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096004"
---
# <a name="use-your-own-certificates-with-data-box-and-data-box-heavy-devices"></a>Použití vlastních certifikátů se zařízeními Data Box a Data Box Heavy

Během zpracování pořadí se certifikáty podepsané svým držitelem generují pro přístup k místnímu webovému uživatelskému rozhraní a úložišti objektů BLOB pro Data Box nebo Data Box Heavy zařízení. Pokud místo toho chcete s vaším zařízením komunikovat přes důvěryhodný kanál, můžete použít vlastní certifikáty.

V tomto článku se dozvíte, jak nainstalovat vlastní certifikáty a jak se vrátit k výchozím certifikátům před vrácením zařízení do datacentra. Obsahuje také souhrn požadavků na certifikáty.

## <a name="about-certificates"></a>Informace o certifikátech

Certifikát poskytuje propojení mezi **veřejným klíčem** a entitou (jako je název domény), která byla **podepsána** (ověřena) důvěryhodnou třetí stranou, jako je například **certifikační autorita**.  Certifikát nabízí pohodlný způsob distribuce důvěryhodných veřejných šifrovacích klíčů. Tímto způsobem certifikáty zajišťují, že vaše komunikace je důvěryhodná a posíláte šifrované informace na správný server.

Při počáteční konfiguraci zařízení Data Box se automaticky vygenerují certifikáty podepsané svým držitelem. Volitelně můžete uvést vlastní certifikáty. Pokud plánujete zahrnout vlastní certifikáty, je třeba postupovat podle pokynů.

Na Data Box nebo Data Box Heavy zařízení se používají dva typy certifikátů koncových bodů:

- Certifikát BLOB Storage
- Místní certifikát uživatelského rozhraní

### <a name="certificate-requirements"></a>Požadavky na certifikáty

Certifikáty musí splňovat následující požadavky:

- Certifikát koncového bodu musí být ve `.pfx` formátu se soukromým klíčem, který se dá exportovat.
- Můžete použít jednotlivý certifikát pro každý koncový bod, certifikát s více doménami pro více koncových bodů nebo certifikát koncového bodu se zástupnými znaky.
- Vlastnosti certifikátu koncového bodu jsou podobné vlastnostem typického certifikátu protokolu SSL.
- V klientském počítači je vyžadován odpovídající certifikát ve formátu DER ( `.cer` Přípona názvu souboru).
- Po nahrání místního certifikátu uživatelského rozhraní bude nutné restartovat prohlížeč a vymazat mezipaměť. Přečtěte si konkrétní pokyny pro váš prohlížeč.
- Pokud se název zařízení nebo název domény DNS změní, musí se certifikáty změnit.
- Při vytváření certifikátů koncových bodů použijte následující tabulku:

  |Typ |Název subjektu (SN)  |Alternativní název subjektu (SAN)  |Příklad názvu subjektu |
  |---------|---------|---------|---------|
  |Místní uživatelské rozhraní| `<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`| `mydevice1.microsoftdatabox.com` |
  |Blob Storage|`*.blob.<DeviceName>.<DNSdomain>`|`*.blob.< DeviceName>.<DNSdomain>`|`*.blob.mydevice1.microsoftdatabox.com` |
  |Jeden certifikát pro více sítí SAN|`<DeviceName>.<DNSdomain>`|`<DeviceName>.<DNSdomain>`<br>`*.blob.<DeviceName>.<DNSdomain>`|`mydevice1.microsoftdatabox.com` |

Další informace najdete v tématu [požadavky na certifikáty](../../articles/databox-online/azure-stack-edge-j-series-certificate-requirements.md).

## <a name="add-certificates-to-device"></a>Přidat certifikáty do zařízení

Pro přístup k místnímu webovému uživatelskému rozhraní a pro přístup k úložišti objektů blob můžete použít vlastní certifikáty.

> [!IMPORTANT]
> Pokud se změní název zařízení nebo doména DNS, musí se vytvořit nové certifikáty. Certifikáty klienta a certifikáty zařízení by se měly aktualizovat pomocí nového názvu zařízení a domény DNS.

Pokud chcete přidat vlastní certifikát do zařízení, postupujte podle těchto kroků:

1. Přejít na **Správa**  >  **certifikátů**.

   **Název** zobrazí název zařízení. **Doména DNS** zobrazuje název domény pro server DNS.

   V dolní části obrazovky se zobrazují aktuálně používané certifikáty. Pro nové zařízení se zobrazí certifikáty podepsané svým držitelem, které se vygenerovaly během zpracování objednávky.

   ![Stránka certifikáty pro zařízení Data Box](media/data-box-bring-your-own-certificates/certificates-manage-certs.png)

2. Pokud potřebujete změnit **název** (název zařízení) nebo **doménu DNS** (doménu serveru DNS pro dané zařízení), udělejte to hned předtím, než certifikát přidáte. Pak vyberte **Použít**.

   Certifikát musí být změněn, pokud se změní název zařízení nebo název domény DNS.

   ![Použití nového názvu zařízení a domény DNS pro Data Box](media/data-box-bring-your-own-certificates/certificates-device-name-dns.png)

3. Chcete-li přidat certifikát, vyberte možnost **Přidat certifikát** a otevřete panel **Přidat certifikát** . Pak vyberte **typ certifikátu** – buď **úložiště objektů BLOB** , nebo **místní webové uživatelské rozhraní**.

   ![Panel Přidat certifikáty na stránce certifikáty pro Data Box zařízení](media/data-box-bring-your-own-certificates/certificates-add-certificate-cert-type.png)

4. Vyberte soubor certifikátu (ve `.pfx` formátu) a zadejte toto heslo, které bylo nastaveno při exportu certifikátu. Pak vyberte **ověřit & přidat**.

   ![Nastavení pro přidání certifikátu koncového bodu objektu blob do Data Box](media/data-box-bring-your-own-certificates/certificates-add-blob-cert.png)

   Po úspěšném přidání certifikátu se na obrazovce **certifikáty** zobrazí kryptografický otisk nového certifikátu. Stav certifikátu je **platný**.

   ![Platný nový certifikát, který se úspěšně přidal](media/data-box-bring-your-own-certificates/certificates-view-new-certificate.png)

5. Podrobnosti o certifikátu zobrazíte tak, že vyberete a kliknete na název certifikátu. Platnost certifikátu skončí po roce.

   ![Zobrazení podrobností o certifikátu pro Data Box zařízení](media/data-box-bring-your-own-certificates/certificates-cert-details.png)

   <!--If you changed the local web UI certificate, you'll see the following error. This error will go away when you install the new certificate on the client computer.

   ![Error after a new Local web UI certificate is added to a Data Box device](media/data-box-bring-your-own-certificates/certificates-unable-to-communicate-error.png) TEST. RESTORE IF ERROR IS REPRODUCED.-->

6. Nainstalujte nový certifikát do klientského počítače, který používáte pro přístup k místnímu webovému uživatelskému rozhraní. Pokyny najdete v části [Import certifikátů do klienta](#import-certificates-to-client)níže.

7. Pokud jste změnili certifikát pro místní webové uživatelské rozhraní, je nutné restartovat prohlížeč a pak místní webové uživatelské rozhraní. Tento krok je nutný, aby nedocházelo k problémům s mezipamětí SSL.

  <!-- TESTING THIS - The communication error should be gone from the **Certificates** screen.-->

## <a name="import-certificates-to-client"></a>Importovat certifikáty do klienta

Po přidání certifikátu do zařízení Data Box musíte certifikát importovat do klientského počítače, který používáte pro přístup k zařízení. Certifikát naimportujete do úložiště Důvěryhodné kořenové certifikační autority pro místní počítač.

Pokud chcete importovat certifikát na klienta Windows, postupujte podle těchto kroků:

1. V Průzkumníku souborů klikněte pravým tlačítkem na soubor certifikátu (s `.cer` formátem) a vyberte **nainstalovat certifikát**. Tato akce spustí Průvodce importem certifikátu.

    ![Importovat certifikát 1](media/data-box-bring-your-own-certificates/import-cert-01.png)

2. V poli **umístění úložiště** vyberte **místní počítač** a pak vyberte **Další**.

    ![V Průvodci importem certifikátu vyberte místní počítač jako umístění úložiště.](media/data-box-bring-your-own-certificates/import-cert-02.png)

3. Vyberte možnost **umístit všechny certifikáty do následujícího úložiště**, vyberte **Důvěryhodné kořenové certifikační autority** a pak vyberte **Další**.

   ![V Průvodci importem certifikátu vyberte úložiště důvěryhodných kořenových certifikačních autorit.](media/data-box-bring-your-own-certificates/import-cert-03.png)

4. Zkontrolujte nastavení a vyberte **Dokončit**. Zobrazí se zpráva s oznámením, že import proběhl úspěšně.

   ![Zkontrolujte nastavení certifikátu a dokončete Průvodce importem certifikátu.](media/data-box-bring-your-own-certificates/import-cert-04.png)

## <a name="revert-to-default-certificates"></a>Vrátit se k výchozím certifikátům

Než vrátíte zařízení do datacentra Azure, měli byste se vrátit k původním certifikátům, které byly vygenerovány během zpracování objednávky.

Chcete-li se vrátit k certifikátům vygenerovaným během zpracování objednávky, postupujte podle následujících kroků:

1. Přejděte na **Správa**  >  **certifikátů** a vyberte **obnovit certifikáty**.

   Vrácení certifikátů se vrátí k použití certifikátů podepsaných svým držitelem, které byly vygenerovány během zpracování objednávky. Ze zařízení se odeberou vaše vlastní certifikáty.

   ![Možnost obnovení certifikátů v nástroji Správa certifikátů pro zařízení Data Box](media/data-box-bring-your-own-certificates/certificates-revert-certificates.png)

2. Po úspěšném dokončení opětovné verze certifikátu přejděte na **vypnout nebo restartovat** a vyberte **restartovat**. Tento krok je nutný, aby nedocházelo k problémům s mezipamětí SSL.

   ![Vypnutí nebo restartování místního webového uživatelského rozhraní po vrácení certifikátů na Data Boxm zařízení](media/data-box-bring-your-own-certificates/certificates-restart-ui.png)

   Počkejte pár minut a pak se znovu přihlaste k místnímu webovému uživatelskému rozhraní.
