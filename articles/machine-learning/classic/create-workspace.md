---
title: 'ML Studio (Classic): vytvoření pracovního prostoru – Azure'
description: Pokud chcete použít Azure Machine Learning Studio (Classic), musíte mít pracovní prostor Machine Learning Studio (Classic). Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 12/07/2017
ms.openlocfilehash: 9caac94c90aa18f8661ab46a0ae72d49500f39cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520540"
---
# <a name="create-and-share-an-machine-learning-studio-classic-workspace"></a>Vytvoření a sdílení pracovního prostoru Machine Learning Studio (Classic)

**platí pro:** ![ Toto je značka zaškrtnutí, což znamená, že se tento článek týká Machine Learning Studio (Classic). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ Toto je X, což znamená, že se tento článek týká Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

Pokud chcete použít Azure Machine Learning Studio (Classic), musíte mít pracovní prostor Machine Learning Studio (Classic). Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.

## <a name="create-a-studio-classic-workspace"></a>Vytvořit pracovní prostor studia (Classic)

Pokud chcete otevřít pracovní prostor v Machine Learning Studio (Classic), musíte být přihlášeni k účtu Microsoft, který jste použili k vytvoření pracovního prostoru, nebo potřebujete získat pozvánku od vlastníka, aby se připojil k pracovnímu prostoru. Z Azure Portal můžete spravovat pracovní prostor, který zahrnuje možnost konfigurace přístupu.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

    > [!NOTE]
    > Pokud se chcete přihlásit a vytvořit pracovní prostor studia (Classic), musíte být správcem předplatného Azure. 
    >
    > 

2. Klikněte na **+ Nový** .

3. Do vyhledávacího pole zadejte **Machine Learning Studio (klasický) pracovní prostor** a vyberte si vyhovující položku. Pak vyberte v dolní části stránky klikněte na **vytvořit** .

4. Zadejte informace o pracovním prostoru:

   - *Název pracovního prostoru* může být až 260 znaků, ale nekončí mezerou. Název nesmí obsahovat tyto znaky: `< > * % & : \ ? + /`
   - *Plán webové služby* , který zvolíte (nebo vytvoříte), spolu s přidruženou *cenovou úrovní* , kterou vyberete, se použije, pokud nasadíte webové služby z tohoto pracovního prostoru.

     ![Vytvořit nový pracovní prostor studia (Classic)](./media/create-workspace/create-new-workspace.png)

5. Klikněte na **Vytvořit**.

   Machine Learning je aktuálně k dispozici v omezeném počtu oblastí. Pokud vaše předplatné neobsahuje jednu z těchto oblastí, může se zobrazit chybová zpráva "nemáte žádná předplatná v povolených oblastech".  Pokud chcete požádat o přidání oblasti do předplatného, vytvořte z Azure Portal novou žádost o podporu od Microsoftu, jako typ problému vyberte **fakturace** a podle pokynů odešlete žádost.


> [!NOTE]
> Machine Learning Studio (Classic) spoléhá na účet služby Azure Storage, který poskytnete k uložení zprostředkujících dat při spuštění pracovního postupu. Po vytvoření pracovního prostoru, pokud se odstraní účet úložiště, nebo pokud se přístupové klíče změní, pracovní prostor přestane fungovat a všechny experimenty v tomto pracovním prostoru selžou.
Pokud nechtěně odstraníte účet úložiště, vytvořte znovu účet úložiště se stejným názvem ve stejné oblasti jako odstraněný účet úložiště a znovu synchronizujte přístupový klíč. Pokud jste změnili přístupové klíče účtu úložiště, znovu je v pracovním prostoru synchronizujte přes portál Azure.

Po nasazení pracovního prostoru ho můžete otevřít v Machine Learning Studio (Classic).

1. Přejděte na Machine Learning Studio (Classic) na adrese [https://studio.azureml.net/](https://studio.azureml.net/) .

2. V pravém horním rohu vyberte příslušný pracovní prostor.

    ![Výběr pracovního prostoru](./media/create-workspace/open-workspace.png)

3. Klikněte na **Moje experimenty**.

    ![Otevřené experimenty](./media/create-workspace/my-experiments.png)

Informace o správě pracovního prostoru studia (Classic) najdete v tématu [Správa pracovního prostoru Azure Machine Learning Studio (Classic)](manage-workspace.md).
Pokud narazíte na problém s vytvořením pracovního prostoru, přečtěte si téma [Průvodce odstraňováním potíží: vytvoření a připojení k pracovnímu prostoru Machine Learning Studio (Classic)](index.yml).


## <a name="share-an-azure-machine-learning-studio-classic-workspace"></a>Sdílení pracovního prostoru Azure Machine Learning Studio (Classic)
Jakmile se vytvoří pracovní prostor Machine Learning Studio (Classic), můžete uživatele pozvat do svého pracovního prostoru a sdílet tak přístup k vašemu pracovnímu prostoru a všem jeho experimentům, datovým sadám atd. Uživatele můžete přidat v jedné ze dvou rolí:

* **Uživatel pracovní** prostor může vytvořit, otevřít, upravit a odstranit experimenty, datové sady atd. v pracovním prostoru.
* **Vlastník** – vlastník může pozvat a odebrat uživatele v pracovním prostoru, a to i to, co může uživatel dělat.

> [!NOTE]
> Účet správce, který vytváří pracovní prostor, se automaticky přidá do pracovního prostoru jako vlastník pracovního prostoru. Dalším správcům nebo uživatelům v tomto předplatném není automaticky udělen přístup k pracovnímu prostoru – je třeba je pozvat explicitně.
> 
> 

### <a name="to-share-a-studio-classic-workspace"></a>Sdílení pracovního prostoru studia (Classic)

1. Přihlaste se k Machine Learning Studio (Classic) na adrese [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Na levém panelu klikněte na **Nastavení** .

3. Klikněte na kartu **Uživatelé** .

4. V dolní části stránky klikněte na **pozvat více uživatelů** .

    ![Nastavení studia](./media/create-workspace/settings.png)

5. Zadejte jednu nebo více e-mailových adres. Uživatelé potřebují platný účet Microsoft nebo účet organizace (z Azure Active Directory).

6. Vyberte, zda chcete přidat uživatele jako vlastníka nebo uživatele.

7. Klikněte na tlačítko pro zaškrtnutí tlačítka **OK** .

Každý přidaný uživatel dostane e-mail s pokyny, jak se přihlásit ke sdílenému pracovnímu prostoru.

> [!NOTE]
> Aby mohli uživatelé v tomto pracovním prostoru nasazovat nebo spravovat webové služby, musí být v předplatném Azure přispěvatel nebo správce. 

## <a name="troubleshoot-storage-accounts"></a>Řešení potíží s účty úložiště


Služba Machine Learning potřebuje účet úložiště pro ukládání dat. Můžete použít existující účet úložiště, nebo můžete vytvořit nový účet úložiště, když vytvoříte nový pracovní prostor Machine Learning Studio (klasický) (Pokud máte kvótu pro vytvoření nového účtu úložiště).

Po vytvoření nového pracovního prostoru Machine Learning Studio (Classic) se můžete přihlásit k Machine Learning Studio (Classic) pomocí účet Microsoft, které jste použili k vytvoření pracovního prostoru. Pokud se zobrazí chybová zpráva "pracovní prostor nebyl nalezen" (podobně jako na následujícím snímku obrazovky), použijte následující postup k odstranění souborů cookie prohlížeče.

![Pracovní prostor nebyl nalezen](media/troubleshooting-creating-ml-workspace/screen3.png)

**Odstranění souborů cookie prohlížeče**

1. Pokud používáte Internet Explorer, klikněte na tlačítko **nástroje** v pravém horním rohu a vyberte **Možnosti Internetu**.  

   ![Možnosti Internetu](media/troubleshooting-creating-ml-workspace/screen4.png)

2. Na kartě **Obecné** klikněte na **Odstranit...**

   ![Karta Obecné](media/troubleshooting-creating-ml-workspace/screen5.png)

3. V dialogovém okně **Odstranit historii procházení** zkontrolujte, že jsou vybrané **soubory cookie a data webu** , a klikněte na **Odstranit**.

   ![Odstranit soubory cookie](media/troubleshooting-creating-ml-workspace/screen6.png)

Po odstranění souborů cookie Restartujte prohlížeč a pak přejdete na stránku [Microsoft Azure Machine Learning Studio (Classic)](https://studio.azureml.net) . Až se zobrazí výzva k zadání uživatelského jména a hesla, zadejte stejné účet Microsoft, jako jste použili k vytvoření pracovního prostoru.


## <a name="next-steps"></a>Další kroky

Další informace o správě pracovního prostoru najdete v tématu [Správa pracovního prostoru Azure Machine Learning Studio (Classic)](manage-workspace.md).