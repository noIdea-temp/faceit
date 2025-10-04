# Faceit FullScreen Script
Script for Face it that add a button with fullscreen functionality.


VideoPreview

![scriptpreview-ezgif com-video-to-gif-converter (1)](https://github.com/user-attachments/assets/6f119d81-75fc-4acf-81c2-36d8ab3e2edf)


// Start HERE

function deepQuerySelector(selector, root = document) {
  const visited = new Set();
  function* nodes(rootNode) {
    if (!rootNode || visited.has(rootNode)) return;
    visited.add(rootNode);
    yield rootNode;

    const tree = (rootNode instanceof Document || rootNode instanceof ShadowRoot)
      ? rootNode
      : rootNode.ownerDocument;
    const walker = tree.createTreeWalker(
      rootNode,
      NodeFilter.SHOW_ELEMENT,
      null
    );
    let cur = rootNode;
    while (cur) {
      yield cur;

      if (cur.shadowRoot) yield* nodes(cur.shadowRoot);
      cur = walker.nextNode();
    }
  }

  for (const n of nodes(root)) {
    if (n.querySelector) {
      const found = n.querySelector(selector);
      if (found) return found;
    }
  }
  return null;
}


function deepQueryInside(container, selector) {
  if (!container) return null;
  const direct = container.querySelector?.(selector) || container.shadowRoot?.querySelector?.(selector);
  if (direct) return direct;
  return deepQuerySelector(selector, container.shadowRoot || container);
}

const btn = document.createElement('button');
btn.textContent = 'Fullscreen';
Object.assign(btn.style, {
  position: 'fixed',
  top: '20px',
  right: '20px',
  zIndex: '999999',
  padding: '10px 14px',
  background: '#e11',
  color: '#fff',
  border: 'none',
  borderRadius: '8px',
  cursor: 'pointer',
  fontSize: '14px'
});
document.body.appendChild(btn);

btn.addEventListener('click', () => {
  const container = deepQuerySelector('[data-testid="video-container"]');

  if (!container) {
    alert('Not found [data-testid="video-container"]');
    return;
  }

  const firstVideoContent = deepQueryInside(container, '[data-testid="video-content"]')
                          || container;

  const videoEl = deepQueryInside(firstVideoContent, 'video,[data-testid="shaka-player"]') || firstVideoContent;

  const target = videoEl; // або firstVideoContent — як тобі потрібно
  if (target.requestFullscreen) target.requestFullscreen();
  else if (target.webkitRequestFullscreen) target.webkitRequestFullscreen();
  else if (target.msRequestFullscreen) target.msRequestFullscreen();
  else alert('Fullscreen API недоступний для цього елемента.');
});
