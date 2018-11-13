---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: ce949caa2b80c08f1015ee21c00197d6a95103c2
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51564853"
---
Pokud chcete doménu, můžete si koupit domény na [portálu pro správu Azure](https://portal.azure.com) přímo. Následujícím postupem nákup názvů domén a přiřaďte do vaší webové aplikace.

1. V prohlížeči se otevře [portálu pro správu Azure](https://portal.azure.com).
2. V **Web Apps** kartu, klikněte na název vaší webové aplikace, vyberte **nastavení**a pak vyberte **vlastní domény a SSL**
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. V **vlastní domény a SSL** okna, klikněte na tlačítko **koupit domény**.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-1.png)
4. V **koupit domény** okna, můžete do textového pole zadejte název domény, které chcete přidat. Navrhované dostupných domén se zobrazí pouze znamená do textového pole. Vyberte doménu chcete koupit.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-2.png)
5. Klikněte na tlačítko **kontaktní údaje** a vyplnit formulář kontaktní údaje domény.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-3.png)
6. Klikněte na tlačítko **vyberte** na **koupit domény** okna a pak se zobrazí informace o nákupu na **potvrzení nákupu** okno. Pokud přijměte právní podmínky a klikněte na tlačítko **koupit**, budou odeslány vaši objednávku a proces nákupu můžete monitorovat na **oznámení**.
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-4.png)
   
   ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-5.png)
7. Pokud jste si objednali úspěšně domény, můžete spravovat domény a přiřadit do vaší webové aplikace. Klikněte na tlačítko **"..."** na pravé straně vaší domény. Pak můžete **zrušit nákup** nebo **spravovat domény**. Klikněte na tlačítko **spravovat domény**, pak můžeme vytvořit vazbu **subdoménu** do naší webové aplikace na **spravovat domény** okno.
   
    ![](./media/custom-dns-web-site/dncmntask-cname-buydomains-6.png)
   
    Po dokončení konfigurace vlastního názvu domény nebude uvedené **vazby názvu hostitele** část vaší webové aplikace.

V tomto okamžiku byste měli moct zadat vlastní název domény v prohlížeči a podívejte se, že je úspěšně přejdete do webové aplikace.

