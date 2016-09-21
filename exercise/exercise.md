#EXERCISE

两个线性表LA,LB，将其合并为一个表，相同元素只有一个

	void union(List &La, List &Lb)
	{
		La_len=ListLength(La);
		Lb_len=ListLength(Lb);
		for(i=1;i<=Lb_len;i++)
		{
			GetElem(Lb,i,e);
			if(! LocateElem(La,e,equal))
				ListInsert(La,++La_len,e);
		}
	}
	求集合；无序

两个线性表元素非递减排列，合并成一个新表，元素仍然非递减

	void MergeList(List &La, List &Lb,List &Lc)
	{
		InitList(Lc);
		i=j=1;k=0;
		La_len=ListLength(La);
		Lb_len=ListLength(Lb);
		while((i<=La_len)&&(j<=Lb_len))
		{
			GetElem(La,i,ai);GetElem(Lb,i,bi);
			if(ai<=bj) 
			{	
				ListInsert(Lc,++k,ai);
				i++;
			}

			else
			{
				ListInsert(Lc,++k,ai);
				j++;
			}
		}
		while(i<=La_len)
		{
			GetElem(La,i++,ai);
			ListInsert(Lc,++k,ai);
		}
		while(j<=Lb_len)
		{
			GetElem(Lb,j++,bj);
			ListInsert(Lc,++k,aj);
		}
	}












































