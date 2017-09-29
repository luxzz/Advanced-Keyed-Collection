public abstract class AdvancedKeyedCollection<TKey, TItem> : KeyedCollection<TKey, TItem>, IDictionary<TKey, TItem>, IDictionary
{
    KeyCollection keys;
    /// <summary>Initializes a new instance of the <see cref="T:AdvancedKeyedCollection`2" /> class that uses the default equality comparer.</summary>
    protected AdvancedKeyedCollection() : this(null, 0)
	{
    }

    /// <summary>Initializes a new instance of the <see cref="T:AdvancedKeyedCollection`2" /> class that uses the specified equality comparer.</summary>
    /// <param name="comparer">The implementation of the <see cref="T:System.Collections.Generic.IEqualityComparer`1" /> generic interface to use when comparing keys, or null to use the default equality comparer for the type of the key, obtained from <see cref="P:System.Collections.Generic.EqualityComparer`1.Default" />.</param>
    protected AdvancedKeyedCollection(IEqualityComparer<TKey> comparer) : this(comparer, 0)
    {
    }

    /// <summary>Initializes a new instance of the <see cref="T:AdvancedKeyedCollection`2" /> class that uses the specified equality comparer and creates a lookup dictionary when the specified threshold is exceeded.</summary>
    /// <param name="comparer">The implementation of the <see cref="T:System.Collections.Generic.IEqualityComparer`1" /> generic interface to use when comparing keys, or null to use the default equality comparer for the type of the key, obtained from <see cref="P:System.Collections.Generic.EqualityComparer`1.Default" />.</param>
    /// <param name="dictionaryCreationThreshold">The number of elements the collection can hold without creating a lookup dictionary (0 creates the lookup dictionary when the first item is added), or –1 to specify that a lookup dictionary is never created.</param>
    /// <exception cref="T:System.ArgumentOutOfRangeException">
    ///   <paramref name="dictionaryCreationThreshold" /> is less than –1.</exception>
    protected AdvancedKeyedCollection(IEqualityComparer<TKey> comparer, int dictionaryCreationThreshold) : base(comparer, dictionaryCreationThreshold)
    {
    }

    public new TItem this[TKey key]
    {
        get
        {
            return base[key];
        }
        set
        {
            SetItemByKey(key, value);
        }
    }

    protected void SetItemByKey(TKey key, TItem value) => SetItem(IndexOf(base[key]), value);
    
    public virtual bool IsReadOnly => Items.IsReadOnly;

    public KeyCollection Keys
    {
        get
        {
            if (this.keys == null)
            {
                keys = new KeyCollection(this);
            }
            return this.keys;
        }
    }

    ICollection<TKey> IDictionary<TKey,TItem>.Keys => Keys;
    ICollection<TItem> IDictionary<TKey, TItem>.Values => this;

    ICollection IDictionary.Keys => Keys;

    ICollection IDictionary.Values => this;

    public virtual bool IsFixedSize => ((IList)this).IsFixedSize;

    object IDictionary.this[object key]
    {
        get
        {
            return base[(TKey)key];
        }
        set
        {
            SetItemByKey((TKey)key, (TItem)value);
        }
    }
    public void ForEach(Action<TItem> action) => ((List<TItem>)Items).ForEach(action);

    public bool TrueForAll(Predicate<TItem> match) => ((List<TItem>)Items).TrueForAll(match);

    protected virtual void SetKeyForItem(TItem item, TKey key)
    {
        //if (typeof(IKeyedItem<TKey>).IsAssignableFrom(item.GetType())) ;
        var tmp = item as IKeyedItem<TKey>;
        if (tmp == null) throw new NotImplementedException(); 
        tmp.Key = key;
    }
    protected override TKey GetKeyForItem(TItem item)
    {
        var tmp = item as IKeyedItem<TKey>;
        if (tmp == null) throw new NotImplementedException();
        return tmp.Key;
    }
    void ICollection<KeyValuePair<TKey, TItem>>.Add(KeyValuePair<TKey, TItem> item) 
        => AddDic(item.Key, item.Value);

    void IDictionary<TKey, TItem>.Add(TKey key, TItem value) => AddDic(key, value);

    private void AddDic(TKey key, TItem value)
    {
        SetKeyForItem(value, key);
        base.Add(value);
    }

    public bool Contains(KeyValuePair<TKey, TItem> item) => Dictionary.Contains(item);

    public bool ContainsKey(TKey key) => base.Contains(key);

    void ICollection<KeyValuePair<TKey, TItem>>.CopyTo(KeyValuePair<TKey, TItem>[] array, int arrayIndex) => Dictionary.CopyTo(array, arrayIndex);

    bool ICollection<KeyValuePair<TKey, TItem>>.Remove(KeyValuePair<TKey, TItem> item) => base.Remove(item.Value);

    public bool TryGetValue(TKey key, out TItem value) => Dictionary.TryGetValue(key, out value);

    IEnumerator<KeyValuePair<TKey, TItem>> IEnumerable<KeyValuePair<TKey, TItem>>.GetEnumerator()
        => Dictionary.GetEnumerator();

    bool IDictionary.Contains(object key) => ContainsKey((TKey)key);

    void IDictionary.Add(object key, object value) => AddDic((TKey)key, (TItem)value);

    IDictionaryEnumerator IDictionary.GetEnumerator() => ((IDictionary)Dictionary).GetEnumerator();

    void IDictionary.Remove(object objKey) => base.Remove((TKey)objKey);

    public class KeyCollection : IList, IList<TKey>
    {
        AdvancedKeyedCollection<TKey, TItem> items;

        public KeyCollection(AdvancedKeyedCollection<TKey, TItem> items)
        {
            this.items = items;
        }

        public TKey this[int index]
        {
            get
            {
              return items.GetKeyForItem(items[index]);
            }

            set
            {
                items.SetKeyForItem(items[index], value);
            }
        }

        object IList.this[int index]
        {
            get
            {
                return this[index];
            }

            set
            {
                
                this[index] =ConvertKey(value);
            }
        }

        private static TKey ConvertKey(object value)
        {
            return(TKey)Convert.ChangeType(value, typeof(TKey));
        }

        public int Count => items.Count;

        public bool IsFixedSize => false;

        public bool IsReadOnly => false;

        public bool IsSynchronized => false;

        public object SyncRoot => ((ICollection)items).SyncRoot;

        public int Add(object value)
        {
            throw new NotSupportedException();
        }

        public void Add(TKey item)
        {
            throw new NotSupportedException();
        }

        public void Clear()
        {
            throw new NotSupportedException();
        }

        public bool Contains(object value)
        {
            return Contains(ConvertKey(value));
        }

        public bool Contains(TKey item) =>
            items.ContainsKey(item);

        public void CopyTo(Array array, int index)
        {
            if (array == null)
            {
                throw new ArgumentNullException(nameof(array));
            }
            if (array.Rank != 1)
            {
                throw new RankException();
            }
            if (array.GetLowerBound(0) != 0)
            {
                throw new IndexOutOfRangeException();
            }
            if (index < 0)
            {
                throw new ArgumentOutOfRangeException(nameof(index));
            }
            int count = Count;
            if (array.Length - index < count)
            {
                throw new IndexOutOfRangeException();
            }
            TKey[] array2 = array as TKey[];
            if (array2 != null)
            {
                CopyTo(array2, index);
                return;
            }
            object[] array3 = array as object[];
            if (array3 == null)
            {
                throw new ArrayTypeMismatchException();
            }
            var entries = this.items.Items;
            for (int i = 0; i < count; i++)
            {
                if (entries[i].GetHashCode() >= 0)
                {
                    array3[index++] = items.GetKeyForItem(entries[i]);
                }
            }
        }

        public void CopyTo(TKey[] array, int index)
        {
            int count = this.Count;
            if (array == null)
            {
                throw new ArgumentNullException(nameof(array));
            }
            if (index < 0 || index > array.Length)
            {
                throw new ArgumentOutOfRangeException(nameof(index));
            }
            if (array.Length - index < count)
            {
                throw new ArgumentException();
            }
            var entries = this.items.Items;
            for (int i = 0; i < count; i++)
            {
                if (entries[i].GetHashCode() >= 0)
                {
                    array[index++] = items.GetKeyForItem(entries[i]);
                }
            }
        }

        public IEnumerator<TKey> GetEnumerator()
        {
            var items = this.items.Items;
            var count = this.Count;
            for (int i = 0; i < count; i++)
            {
                yield return this.items.GetKeyForItem(items[i]);
            }
        }

        public int IndexOf(object value)
        {
            throw new NotImplementedException();
        }

        public int IndexOf(TKey item)
        {
            return items.IndexOf(items[item]);
        }

        public void Insert(int index, object value)
        {
            throw new NotSupportedException();
        }

        public void Insert(int index, TKey item)
        {
            throw new NotSupportedException();
        }

        public void Remove(object value)
        {
                Remove(ConvertKey(value));

        }

        public bool Remove(TKey item)
        {
            return items.Remove(item);
        }

        public void RemoveAt(int index)
        {
            items.RemoveAt(index);
        }

        IEnumerator IEnumerable.GetEnumerator()
        {
            return GetEnumerator();
        }
    }


}
public interface IKeyedItem<TKey>
{
    TKey Key { get; set; }
}

