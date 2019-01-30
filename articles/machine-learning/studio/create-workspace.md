---
Název: Vytvořte pracovní prostor titleSuffix: Azure Machine Learning Studio Popis: Pokud chcete používat Azure Machine Learning Studio, musíte mít pracovní prostor Machine Learning Studio. Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.
Services: machine learningu ms.service: ms.subservice strojového učení: studio ms.topic: článku

Autor: ericlicoding ms.author: amlstudiodocs ms.custom: seodec18 ms.date: 12/07/2017
---

# <a name="create-and-share-an-azure-machine-learning-studio-workspace"></a>Vytváření a sdílení pracovního prostoru služby Azure Machine Learning Studio

Pokud chcete používat Azure Machine Learning Studio, musíte mít pracovní prostor Machine Learning Studio. Tento pracovní prostor obsahuje nástroje potřebné k vytváření, správě a publikování experimentů.



### <a name="to-create-a-workspace"></a>Vytvořit pracovní prostor
1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com/).

    > [!NOTE]
    > Přihlaste se a vytvořit pracovní prostor, musíte být správcem předplatného Azure. 
    >
    > 

2. Klikněte na tlačítko **+ nová**

3. Do vyhledávacího pole zadejte **pracovní prostor Machine Learning Studio** a vyberte odpovídající položky. Potom klikněte na vybrat **vytvořit** v dolní části stránky.

4. Zadejte informace o pracovním prostoru:

    - *Název pracovního prostoru* může být až 260 znaků, není koncovou mezerou. Název nemůže obsahovat tyto znaky: `< > * % & : \ ? + /`
    - *Plán web service* vyberete (nebo vytvoření), spolu s přidruženou *cenovou úroveň* vybrat, se používá při nasazení webové služby z tohoto pracovního prostoru.

    ![Vytvořit nový pracovní prostor](./media/create-workspace/create-new-workspace.png)

5. Klikněte na možnost **Vytvořit**.

Po nasazení se pracovní prostor, lze jej otevřít v nástroji Machine Learning Studio.

1. Procházení k sadě Machine Learning Studio [ https://studio.azureml.net/ ](https://studio.azureml.net/).

2. V pravém horním rohu vyberte svůj pracovní prostor.

    ![Výběr pracovního prostoru](./media/create-workspace/open-workspace.png)

3. Klikněte na tlačítko **Moje experimenty**.

    ![Otevřít experimentů](./media/create-workspace/my-experiments.png)

Informace o správě pracovního prostoru najdete v tématu [Správa pracovního prostoru Azure Machine Learning](manage-workspace.md).
Pokud narazíte na problém s vytvořením pracovního prostoru, přečtěte si téma [Průvodce odstraňováním potíží: Vytvoření a připojení k pracovnímu prostoru Machine Learning](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Sdílení pracovního prostoru služby Azure Machine Learning
Jednou Machine Learning při vytváření pracovního prostoru, můžete uživatele pozvat do pracovního prostoru pro sdílení přístupu do vašeho pracovního prostoru a všechny jeho experimentů, datových sad, poznámkových bloků, atd. Můžete přidat uživatele v jednom ze dvou rolí:

* **Uživatel** -uživatel pracovní prostor může vytvoření, otevření, úprava a odstranění experimenty, datové sady atd., v pracovním prostoru.
* **Vlastník** – může pozvat vlastník a odebírání uživatelů v pracovním prostoru, kromě uživatele, můžete provést.

> [!NOTE]
> Účet správce, který vytvoří pracovní prostor se automaticky přidá do pracovního prostoru jako vlastníka pracovního prostoru. Ale jiní správci nebo uživatelé v tomto předplatném nejsou automaticky udělí přístup k pracovnímu prostoru – je potřeba explicitně pozvánku.
> 
> 

### <a name="to-share-a-workspace"></a>Ke sdílení pracovního prostoru

1. Přihlaste se k sadě Machine Learning Studio [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Na levém panelu klikněte na tlačítko **nastavení**

3. Klikněte na tlačítko **uživatelé** kartu

4. Klikněte na tlačítko **POZVAT další uživatele** v dolní části stránky

    ![Nastavení sady Studio](./media/create-workspace/settings.png)

5. Zadejte jeden nebo více e-mailové adresy. Uživatelé potřebují platný účet Microsoft nebo účtu organizace (z Azure Active Directory).

6. Vyberte, zda chcete přidat uživatele jako vlastníka nebo uživatele.

7. Klikněte na tlačítko **OK** značku zaškrtnutí.

Každý uživatel, kterého přidáte obdrží e-mail s pokyny, jak se přihlásit do sdíleného pracovního prostoru.

> [!NOTE]
> Pro uživatele, abyste mohli nasazovat nebo spravovat webové služby v tomto pracovním prostoru musí být přispěvatelem nebo správcem v rámci předplatného Azure. 



