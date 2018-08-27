V tomto názorném postupu se dozvíte, jak používat **Salesforce – když je vytvořen objekt** trigger pro zahájení pracovní postup aplikace logiky, když se vytvoří nový zájemce v Salesforce.

> [!NOTE]
> Výzva pro přihlášení ke svému účtu Salesforce, pokud jste ještě nevytvořili *připojení* do Salesforce.  
> 
> 

1. Zadejte *salesforce* do vyhledávacího pole v návrháři pro logic apps zvolte **Salesforce – když je vytvořen objekt** aktivační události.  
   ![Bitová kopie aktivační události Salesforce 1](./media/connectors-create-api-salesforce/trigger-1.png)   
2. **Při vytvoření objektu** se zobrazí ovládací prvek.  
   ![Obrázek aktivační události Salesforce 2](./media/connectors-create-api-salesforce/trigger-2.png)   
3. Vyberte **typ objektu** vyberte *vést* ze seznamu objektů. V tomto kroku jsou označující, že vytvoříte aktivační událost, která upozorní aplikaci logiky pokaždé, když se vytvoří nový zájemce v Salesforce.   
   ![Obrázek aktivační události Salesforce 3](./media/connectors-create-api-salesforce/trigger-3.png)   
4. A je to. Vytvoříte aktivační událost. Musíte však vytvořit alespoň jednu akci, aby bylo možné vytvořit aplikaci logiky platný.    
   ![Obrázek aktivační události Salesforce 4](./media/connectors-create-api-salesforce/trigger-4.png)   

V tomto okamžiku byla nakonfigurována svou aplikaci logiky s triggerem, který se začne spustit z jiné triggery a akce v pracovním postupu je vytvořena nová položka v Salesforce.  

