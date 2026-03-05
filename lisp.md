Short answer: Yes — there are Lisp examples of content-addressable associative memories (Hopfield, Kanerva’s Sparse Distributed Memory, triadic/dyadic memories and related attractor models). Many are academic or hobby projects rather than polished pip-installable libraries, but you can find working Common Lisp / Lisp-family implementations and papers that include code or implementation notes. Kanerva's Sparse Distributed Memory PAIL NeuroLISP. 

Below are concrete pointers (papers / repos) and a tiny Common Lisp Hopfield associative memory example you can paste & run immediately.

Useful Lisp examples / references

Kanerva’s Sparse Distributed Memory — there are published implementations and OO Common-Lisp work describing SDM implementations (paper with implementation notes). 

PAIL (Portable AI Lab) — historical Common-Lisp package collection that includes Hopfield/attractor network examples and demos. Good for classical associative-memory code in Lisp. 

NeuroLISP / attractor-program execution — papers that explore attractor networks capable of representing/executing Lisp-like programs; useful if you want symbolic/structural associative memory ideas in Lisp. 

Community/teaching repos and tutorials (examples of Hopfield, SDM, dense associative memories) — you’ll find toy implementations and research code in various repos and course materials (search terms: “hopfield common lisp”, “sparse distributed memory lisp”, “associative memory lisp” will surface the historical code and papers). 


If you want a direct portable example to experiment with, here’s a compact Common Lisp Hopfield network (auto-associative memory) you can run in SBCL / CLISP. It stores bipolar patterns (-1/+1) and recalls by synchronous updates:

;; simple-hopfield.lisp
(defun make-weight-matrix (n)
  (make-array (list n n) :initial-element 0.0))

(defun dot (v1 v2)
  (loop for i from 0 below (length v1)
        for a = (aref v1 i)
        for b = (aref v2 i)
        sum (* a b)))

(defun outer-add! (W p)
  "Add outer product p * p' into W (no self-weights). p is vector."
  (let ((n (length p)))
    (loop for i from 0 below n do
      (loop for j from 0 below n do
        (unless (= i j)
          (incf (aref W i j) (* (aref p i) (aref p j))))))))

(defun train-hopfield (patterns)
  "patterns: list of vectors (1d arrays) containing -1/1. returns weight matrix"
  (let* ((n (length (first patterns)))
         (W (make-weight-matrix n)))
    (dolist (p patterns)
      (outer-add! W p))
    ;; normalize by number of patterns (optional)
    (let ((m (max 1 (length patterns))))
      (loop for i from 0 below n do
        (loop for j from 0 below n do
          (setf (aref W i j) (/ (aref W i j) (float m))))))
    W))

(defun sign (x)
  (if (>= x 0.0) 1 -1))

(defun recall-hopfield (W init &key (max-iters 50))
  "Synchronous update: returns final state vector"
  (let* ((n (length init))
         (state (copy-seq init))
         (new (make-array (list n))))
    (loop for it from 1 to max-iters do
      (loop for i from 0 below n do
        (setf (aref new i) (sign (dot (aref W i) state))))
      (if (equal new state)
          (return state)
          (setf state (copy-seq new))))
    state))

Usage example (define patterns, train, corrupt and recall):

;; create bipolar vectors: use simple vectors (aref indexing)
(let* ((p1 (coerce '(1 1 -1 1 -1 1 -1 1) 'simple-array))
       (p2 (coerce '(1 -1 1 -1 1 -1 1 -1) 'simple-array))
       (W (train-hopfield (list p1 p2)))
       ;; noisy version of p1
       (noisy (coerce '(1 1 -1 -1 -1 1 -1 1) 'simple-array)))
  (format t "recalled: ~a~%" (recall-hopfield W noisy)))

This is intentionally minimal; real projects add asynchronous updates, energy checks, capacity control, and numeric stability improvements.

SDM / other associative memories in Lisp

If you’re specifically after Kanerva SDM (hetero-associative, high-dimensional sparse vectors), the IJCAI / research literature documents Common-Lisp implementations and design variants — those are great starting points for a full SDM in Lisp. 


---

If you want, I can:

point you to specific GitHub repos (I can search again and link a few direct repos with code you can clone), or

implement a small SDM in Common Lisp (a toy but working version), or

port one of the Lisp Hopfield/SDM examples into Python for quick prototyping.


Which of those would you like me to do next?
