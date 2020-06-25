# one_double_linkedlist
// LinkedList.cpp: определяет точку входа для консольного приложения.
//

#include <iostream>
#include <algorithm>



using namespace std;

class Element
{
	//элемент связного списка
private:
	//указатель на предыдущий и следующий элемент
	Element* next;
	Element* prev;

	//информация, хранимая в поле
	double field;
public:
	//доступ к полю *next
	virtual Element* getNext() { return next; }
	virtual void setNext(Element* value) { next = value; }

	//доступ к полю *prev
	virtual Element* getPrevious() { return prev; }
	virtual void setPrevious(Element* value) { prev = value; }

	//доступ к полю с хранимой информацией field

	virtual double getValue() { return field; }
	virtual void setValue(double value) { field = value; }
};

class LinkedListParent
{
protected:
	//достаточно хранить только начало
	Element* begin;
	//для удобства храним количество элементов
	int num;
public:
	virtual int Number() { return num; }

	virtual Element* getBegin() { return begin; }
	virtual void setBegin(Element* value) { begin = value; }

	LinkedListParent()
	{
		//конструктор без параметров
		cout << "\nParent constructor";
		begin = NULL;
		num = 0;
	}
	//Оператор =
	LinkedListParent& operator=(const LinkedListParent& L)
	{
		cout << "\nOperator =";
		Element* cur = L.begin;
		while (cur != NULL)
		{
			this->push(cur->getValue());
			cur = cur->getNext();
		}
		return*this;
	}
	//Конструктор копий
	LinkedListParent(const LinkedListParent& L)
	{
		cout << "\nCopy constructor";
		Element* cur = L.begin;
		while (cur != NULL)
		{
			this->push(cur->getValue());
			cur = cur->getNext();
		}
	}
	//Положить элемент в конец
	virtual Element* push(double a)
	{

		Element* cur = begin;
		if (begin == NULL)
		{
			//ничего в списке нет
			num = 1;
			begin = new Element;
			begin->setValue(a);
			begin->setNext(NULL);
			return begin;
		}
		//ищем последний элемент 
		while (cur->getNext() != NULL) //error
		{
			cur = cur->getNext();
		}
		num++;
		//вставка элемента
		cur->setNext(new Element);
		cur->getNext()->setValue(a);
		cur->getNext()->setNext(NULL);

		return cur->getNext();
	}
	//Положить элемент в конец - рекурсивная версия
	virtual Element* push_r(double a, Element* current = NULL)
	{
		//в списке нет элементов - цепляем в начало добавляемый элемент
		if (begin == NULL)
		{
			//ничего в списке нет
			num = 1;
			begin = new Element;
			begin->setValue(a);
			begin->setNext(NULL);
			return begin;
		}
		//ничего не передано в функцию - по умолчанию начинаем поиск свободного места с начала списка
		if (current == NULL)
			current = begin;

		//мы находимся где-то в списке - ищем последний элемент рекурсивно
		if (current->getNext() != NULL)
			return push_r(a, current->getNext());
		//указатель на следующий элемент свободен - цепляем туда новый. Здесь рекурсия закончится 
		if (current->getNext() == NULL)
		{
			current->setNext(new Element);
			current->getNext()->setValue(a);
			current->getNext()->setNext(NULL);
			num++;
			return current->getNext();
		}
	}
	virtual double pop()
	{
		//вытащить элемент с конца
		Element* cur = begin;
		Element* prev = NULL;
		//отдельно рассмотрим случай 0/1 элементов
		if (begin == NULL)
		{
			return -1;
		}
		if (num == 1)
		{
			double res = begin->getValue();
			num--;
			return res;
		}
		//ищем последний и предпоследний элемент
		while (cur->getNext() != NULL)
		{
			prev = cur;
			cur = cur->getNext();
		}
		//сохраняем результат
		double res = cur->getValue();
		//предпоследний стал последним
		prev->setNext(NULL);
		//последнего стёрли
		delete cur;
		//число элементов снизилось на 1	
		num--;
		//возвращаем удаленный элемент
		return res;
	}


	~LinkedListParent()
	{
		//деструктор - освобождение памяти
		cout << "\nParent destructor";

	}
	virtual Element* operator[](int i)
	{
		//индексация
		if (i<0 || i>num) return NULL;
		int k = 0;

		//ищем i-й элемент - вставем в начало и отсчитываем i шагов вперед
		Element* cur = begin;
		for (k = 0; k < i; k++)
		{
			cur = cur->getNext();
		}
		return cur;
	}

	virtual void print()
	{
		//итеративная версия вывода списка - стандартный цикл перебора всех элементов
		Element* cur = begin;
		while (cur != NULL)
		{
			cout << cur->getValue() << " ";
			cur = cur->getNext();
		}
	}

	virtual void print_r(Element* current = NULL)
	{
		//рекурсивная версия вывода списка 
		if (current == NULL)
			current = begin;
		if (current != NULL)
		{
			cout << current->getValue() << "\n";
			//применяем функцию к следующему элементу
			if (current->getNext() != NULL)
				print_r(current->getNext());
		}

	}
};

class LinkedListChild : public LinkedListParent
{
public:
	LinkedListChild() : LinkedListParent() { cout << "\nChild constructor"; }

	~LinkedListChild()
	{
		cout << "\nChild destructor";

	}
	//Вставка элемента со значением value в список сразу за элементом current
	virtual Element* insert(Element* current, double value)
	{

		Element* newElem = new Element;
		newElem->setNext(NULL);
		newElem->setValue(value);
		return insert(current, newElem);

	}
	//Вставка элемента newElem в список сразу за элементом current
	virtual Element* insert(Element* current, Element* newElem)
	{
		if (current == NULL)
		{

			if (begin == NULL)
				return NULL;

			current = begin;
			while (current->getNext() != NULL)//ищем последний, если список не пустой
			{
				current = current->getNext();
			}
			current->setNext(newElem);
			newElem->setNext(current->getNext());
			num++;

		}

		//список не пустой, current!=NULL
		newElem->setNext(current->getNext());
		current->setNext(newElem);
		num++;
		return current->getNext();
	}

	void remove(Element* current)
	{
		if (current == begin)
		{
			begin = begin->getNext();
			num--;
			delete current;
		}
		else if (current != NULL)
		{
			//предыдущий элемент
			Element* cur = begin;
			while (cur->getNext() != current)
			{
				cur = cur->getNext();//cur-предыдущий элемент,
			}
			cur->setNext(current->getNext());
			num--;
			delete current;
		}
	}

	virtual Element* find(double value) //поиск 
	{
		Element* cur = begin;
		while (cur != NULL)
		{
			if (cur->getValue() == value)
				break;
			cur = cur->getNext();
		}
		if (cur != NULL) return cur;
		else
			return NULL;

	}
};


class DoubleSidedLinkedList :public LinkedListChild
{
protected: Element* end;
public:
	DoubleSidedLinkedList() :LinkedListChild() { cout << "\nDoubleSidedLinkedList constructor"; end = NULL; }
	~DoubleSidedLinkedList()
	{
		cout << "\nDoubleSidedLinkedList destructor";

	}
	virtual Element* GetEnd() { return end; }
	virtual void SetEnd(Element* value) { end = value; }
	//Добавление нового элемента в конец
	virtual Element* push(double value)
	{
		Element* added_element = LinkedListChild::push(value);
		if (end != NULL) //если список заполнен элементами
		{

			end->setNext(added_element);
			added_element->setPrevious(end);
			end = added_element;
		}
		else  //если список пустой,значит добавляем новый элемент
		{
			end = added_element;
		}
		return end;
	}
	//рекурсивная версия
	virtual Element* push_r(double value, Element* current = NULL)
	{


		if (end == NULL)//если список пустой,добавляем новый элемент в конец
		{
			num = 1;
			end = new Element;
			end->setValue(value);
			begin = end;
			end->setNext(NULL);
			return end;
		}
		//ничего не передано в функцию -  начинаем поиск свободного места с конца  списка
		if (current == NULL)
			current = end;

		//мы находимся где-то в списке - ищем последний элемент рекурсивно
		if (current->getNext() != NULL)
			return push_r(value, current->getNext());
		while (end->getNext() != NULL)
		{
			end = end->getNext();
		}
		Element* newEl = new Element;
		end->setNext(newEl);/*new Element*/
		end->getNext()->setValue(value);
		newEl->setPrevious(end);
		newEl->setNext(NULL);
		end = newEl;
		num++;
		return end->getNext();

	}
	//Удаление элемента с конца и возвращение его из функции
	virtual double pop()
	{
		Element* cur = end;
		Element* prev = NULL;
		//отдельно рассмотрим случай 0/1 элементов
		if (end == NULL)
		{
			return -1;
		}
		if (num == 1)
		{
			double res = end->getValue();
			num--;
			return res;
		}
		if (end->getNext() == NULL)
		{
			prev = end->getPrevious();
		}
		double res = end->getValue();//сохраняем результат		
		prev->setNext(NULL); //предпоследний стал последним
		delete end;
		end = prev;
		num--;
		return res;
	}
	//Удаление элемента
	void remove(Element* current)
	{
		if (current != NULL)
		{

			if (current == begin)//если элемент,который надо удалить-первый
			{

				begin = begin->getNext();
				num--;
				delete current;
			}
			if (current == end)//если элемент,который надо удалить-последний
			{
				end = end->getPrevious();
				end->setNext(NULL);
				num--;
				delete current;
			}
			else
			{
				Element* previous = begin;
				while (previous->getNext() != current)
				{
					previous = previous->getNext();
				}
				previous->setNext(current->getNext());
				num--;
				delete current;
			}
		}

	}
	//вставка элемента в некоторое место в списке(заданное указателем)
	virtual Element* insert(Element* current, double value)
	{
		if (current == NULL)
		{
			Element* insert_elem = push(value);
			return insert_elem;
		}
		else
		{
			Element* insert_elem = LinkedListChild::insert(current, value);
			if (insert_elem->getNext() != NULL)
			{
				insert_elem->getNext()->setPrevious(insert_elem);
			}
			insert_elem->setPrevious(current);

			if (current == end)
				end = insert_elem;

			return insert_elem;
		}

	}
	//поиск элемента
	virtual Element* find(double value_to_find)
	{
		Element* cur = begin;
		while (cur != NULL)
		{
			if (cur->getValue() == value_to_find)
				return cur;
			cur = cur->getNext();
		}
		return NULL;
	}


	void merge(DoubleSidedLinkedList* l1, DoubleSidedLinkedList* l2) //слияние 2х списков
	{
		if (l1 == NULL) cout << "\nСписок1 пустой." << endl;
		if (l2 == NULL) cout << "\nСписок2 пустой." << endl;
		else
		{
			begin = l1->getBegin();
			l1->GetEnd()->setNext(l2->getBegin());
			l2->getBegin()->setPrevious(l1->GetEnd());
			end = l2->GetEnd();
			num = l1->Number() + l2->Number();
		}
	}

	void split(Element* delimiter, DoubleSidedLinkedList* l1, DoubleSidedLinkedList* l2)//разделение списка 
	{
		if (l1 == NULL) cout << "\nДанный список пустой." << endl;
		if (delimiter == NULL) cout << "\nМесто разделения не было указано." << endl;
		else
		{

			l2->setBegin(delimiter->getNext());
			l2->getBegin()->setPrevious(NULL);
			l2->SetEnd(l1->GetEnd());
			l2->GetEnd()->setPrevious(l1->GetEnd()->getPrevious());
			l1->SetEnd(delimiter);
			l1->GetEnd()->setPrevious(delimiter->getPrevious());
			l1->GetEnd()->setNext(NULL);

		}
	}

};

int main()
{
	if (1)
	{


		setlocale(LC_ALL, "Rus");

		LinkedListParent L;
		LinkedListChild  L1;

		for (int i = 0; i < 7; i++)
		{
			L1.push_r(rand() % 13);
		}
		cout << "\nChild.List1: ";
		L1.print();

		cout << "\nRemove.Child: ";
		L1.remove(L1.getBegin());
		L1.print();
		cout << "\nFind.Child: ";
		L1.print();
		Element* find = L1.find(5);
		if (find != NULL) cout << "\nElement " << find->getValue() << "found:" << find << endl;
		else cout << "\nElement not found" << endl;
		cout << "Insert.Child:";
		L1.insert(L1.find(6), 77);
		L1.print();
		cout << "\n";

		DoubleSidedLinkedList L2;
		for (int i = 0; i < 4; i++)
		{
			L2.push_r(i * 2 + 5);
		}
		cout << "\nDoubleList2:" << " ";
		L2.print();
		cout << "\nFind.Double: ";
		L2.print();
		Element* find1 = L2.find(4);
		if (find != NULL) cout << "\nElement " << find->getValue() << "found:" << find1 << endl;
		else cout << "\nElement not found" << endl;


		DoubleSidedLinkedList L3;
		for (int i = 0; i < 6; i++)
		{
			L3.push_r(8 + i * 3);
		}
		cout << "\nDouble.List3: ";
		L3.print();

		DoubleSidedLinkedList L4;

		DoubleSidedLinkedList L5;
		cout << "\nList1 ";
		L1.print();
		L1.insert(L1.getBegin(), 13);
		cout << "\nList1 after insert element: ";
		L1.print();
		cout << '\n';

		cout << "\nList2: ";
		L2.print();
		L2.insert(L2.GetEnd(), 8);
		cout << "\nList2 after insert element: ";
		L2.print();
		cout << "\nPop.Double:" << L2.pop() << endl;
		L2.print();
		cout << '\n';

		cout << "\nMerge.Double: ";
		cout << "\nList2: ";
		L2.print();
		cout << "\nList3: ";
		L3.print();

		L4.merge(&L2, &L3);
		cout << "\nList2 + List3 = "; 
		L4.print();

		cout << "\natfer insert to (List2 + List3): ";
		L4.insert(L4.getBegin(), 55);
		L4.print();

		cout << "\nSplit.Double:";
		L4.print();

		L4.split(L4.find(9), &L4, &L5);
		cout << "\nList4 = ";
		L4.print();

		cout << "\nList5= ";
		L5.print();

	}

	return 0;
}



