# BANNED: `useEffect`

**Direct usage of `useEffect` is BANNED in this codebase.** 

Most `useEffect` usage compensates for something React already provides better primitives for. Banning the hook forces logic to be declarative, predictable, and event-driven, preventing race conditions, infinite loops, and dependency hell.

## The 5 Rules for Replacing `useEffect`

### Rule 1: Derive state, do not sync it
Compute values inline during the render cycle. Do not use state to mirror other state or props.
```typescript
// ❌ BAD
const [tax, setTax] = useState(0);
useEffect(() => setTax(subtotal * 0.1), [subtotal]);

// ✅ GOOD
const tax = subtotal * 0.1;
```

### Rule 2: Use data-fetching libraries
Never use `useEffect` to fetch data and call `setState`. It creates race conditions and lacks caching. Use a query library (e.g., React Query, SWR, or framework-specific loaders).
```typescript
// ❌ BAD
useEffect(() => { fetchProduct(id).then(setProduct) }, [id]);

// ✅ GOOD
const { data: product } = useQuery(['product', id], () => fetchProduct(id));
```

### Rule 3: Event handlers, not effects
Do not use state flags to trigger side effects. If an action is caused by a user interaction, do the work directly in the event handler.
```typescript
// ❌ BAD: State as an action relay
const [liked, setLiked] = useState(false);
useEffect(() => { if (liked) { postLike(); setLiked(false); } }, [liked]);

// ✅ GOOD
<button onClick={() => postLike()}>Like</button>
```

### Rule 4: `useMountEffect` for one-time external sync
For the rare case where you *must* sync with an external system (DOM integration, third-party widgets, browser API subscriptions), use the custom `useMountEffect` hook. 

**Never put condition guards inside the effect.** Instead, mount the component conditionally so the effect only runs when preconditions are met.
```typescript
export function useMountEffect(effect: () => void | (() => void)) {
  /* eslint-disable no-restricted-syntax */
  useEffect(effect, []);
}

// ❌ BAD: Guard inside effect
function VideoPlayer({ isLoading }) {
  useEffect(() => { if (!isLoading) playVideo(); }, [isLoading]);
}

// ✅ GOOD: Mount conditionally
function VideoPlayerWrapper({ isLoading }) {
  if (isLoading) return <LoadingScreen />;
  return <VideoPlayer />; // Inside: useMountEffect(() => playVideo())
}
```

### Rule 5: Reset with `key`, not dependency choreography
When you need a component to "start fresh" because an ID or entity changed, rely on React's remount semantics. Do not write effects that watch an ID and reset local state.
```typescript
// ❌ BAD: Effect emulates remount behavior
useEffect(() => { loadVideo(videoId); }, [videoId]);

// ✅ GOOD: `key` forces clean remount
function VideoPlayerWrapper({ videoId }) {
  return <VideoPlayer key={videoId} videoId={videoId} />;
}
```

## Architectural Forcing Function
Banning `useEffect` forces cleaner tree design:
- Parents own orchestration and lifecycle boundaries.
- Children can assume preconditions are already met.
- Each unit does one job, and coordination happens at clear boundaries.
