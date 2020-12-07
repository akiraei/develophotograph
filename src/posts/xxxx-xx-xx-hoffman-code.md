```js
const string = `
Lorem ipsum dolor sit amet, consectetur adipiscing elit. Etiam mattis eros nec ullamcorper ultricies. Donec facilisis purus tincidunt congue euismod. Aliquam rutrum dapibus malesuada. Curabitur posuere ante justo, ut dictum mi vulputate nec. Nam odio enim, dignissim in efficitur eleifend, lacinia nec nulla. Praesent ut fermentum arcu. Nunc feugiat eget elit vitae laoreet. Nullam id elit at ipsum auctor mattis. In sed lobortis augue. Nam et justo dapibus, tristique nulla aliquam, aliquet diam. Pellentesque pretium suscipit eros id aliquet. In semper arcu in lectus vestibulum, in interdum mi sollicitudin. Fusce vel sem vel nibh euismod fermentum eget at augue. Mauris nec fringilla purus. Etiam hendrerit efficitur nibh, at pretium dui euismod sit amet. Nulla sit amet elit elementum, dapibus arcu ut, ornare mauris.

Integer id enim purus. Etiam ut maximus nibh. Duis faucibus, nisl sed imperdiet mollis, sem augue sagittis lorem, vitae interdum ante lorem nec nulla. Nam feugiat rhoncus placerat. Sed sit amet feugiat purus. Nullam commodo bibendum rutrum. Nam posuere efficitur elit. Cras consequat venenatis velit, ac viverra turpis semper ut.

Donec posuere neque in tortor consectetur porta. Nam vulputate quis nibh a mollis. Aenean eu leo facilisis, consequat sem vitae, venenatis orci. Aliquam purus nisi, feugiat vitae sagittis nec, volutpat vel tortor. Etiam ut ligula luctus, consectetur eros ut, commodo nibh. Duis malesuada ex lorem, sed fringilla lacus vulputate fringilla. Morbi sed aliquet leo. Maecenas vel condimentum nisi. Nullam vel justo in dui commodo feugiat. Donec pellentesque imperdiet neque nec vulputate. Donec dignissim sapien dui, ut fermentum lacus ultrices sed. In hac habitasse platea dictumst. Integer feugiat, lectus in hendrerit pharetra, nisi leo sollicitudin metus, in efficitur elit massa vel purus. Nam pellentesque mauris blandit feugiat dapibus. Nullam posuere laoreet gravida.

Nullam quis risus a diam tincidunt semper. Duis bibendum tincidunt augue in fermentum. Morbi lacinia nec orci vel bibendum. Donec eget egestas metus. Maecenas ac ullamcorper orci. Donec nec lobortis ipsum. Praesent sodales est vitae ligula suscipit convallis. Morbi et posuere mi. Aliquam at sollicitudin lorem. Nullam eget est lectus.

Nullam placerat urna et placerat semper. Aenean semper laoreet tellus, nec pulvinar lectus maximus ut. Phasellus consequat tortor dui, sed tincidunt sapien laoreet vel. Sed pharetra dapibus dapibus. Nulla sodales libero non massa tempus, at commodo odio condimentum. Morbi hendrerit sapien et risus dignissim suscipit. Etiam ultricies venenatis nisl id maximus. Praesent vel viverra velit.`;

class Node {
  constructor(c, r) {
    this.left = null;
    this.right = null;
    this.root = null;
    this.character = c;
    this.rate = r;
    this.code = "";
    this.value = null;
    this.stack = 1;
  }
  setLeft(node) {
    this.left = node;
  }
  setRight(node) {
    this.right = node;
  }
  setRoot(node) {
    this.root = node;
  }
  setCode(code) {
    this.code = code;
  }
  setStack(stack) {
    this.stack = stack;
  }
  setValue(value) {
    this.value = value;
  }
}

const getEntrophy = string => {
  const newSet = new Set(string);
  const length = string.length;
  const object = {};
  const array = [];
  newSet.forEach(e => {
    object[e] = 0;
  });
  for (let i = 0; i < string.length; i++) {
    const c = string[i];
    object[c] = object[c] + 1;
  }
  newSet.forEach(e => {
    object[e] = object[e] / length;
  });
  Object.keys(object).forEach(key => {
    array.push({ key: key, value: object[key] });
  });

  return array
    .sort((a, b) => a.value - b.value)
    .map(e => {
      return new Node(e.key, e.value);
    });
};

const getTree = arr => {
  if (arr[0] && arr[1]) {
    const newNode = new Node(null, arr[0].rate + arr[1].rate);
    arr[1].setRoot = newNode;
    arr[1].setValue("0");
    newNode.setLeft(arr[1]);
    arr[0].setRoot = newNode;
    arr[0].setValue("1");
    newNode.setRight(arr[0]);
    newNode.setStack(
      arr[0].stack > arr[1].stack ? arr[0].stack + 1 : arr[1].stack + 1
    );
    const array = [newNode];
    return getTree(
      array
        .concat(arr.slice(2))
        .sort((a, b) => a.rate - b.rate)
        .sort((a, b) => a.stack - b.stack)
    );
  } else {
    return arr;
  }
};

const arr = getEntrophy(string);
const noode = getTree(arr);
console.log(noode);
```
