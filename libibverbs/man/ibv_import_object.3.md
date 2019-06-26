---
date: 2018-06-26
footer: libibverbs
header: "Libibverbs Programmer's Manual"
layout: page
license: 'Licensed under the OpenIB.org BSD license (FreeBSD Variant) - See COPYING.md'
section: 3
title: ibv_export_to_fd
tagline: Verbs
---

# NAME

**ibv_export_to_fd**, **ibv_import_pd**, **ibv_import_mr** - export & import ib hw objects.

# SYNOPSIS

```c
#include <infiniband/verbs.h>

int ibv_export_to_fd(uint32_t fd,
                     uint32_t *new_handle,
                     struct ibv_context *context,
                     enum uverbs_default_objects type,
                     uint32_t handle);

struct ibv_pd *ibv_import_pd(struct ibv_context *context,
                             uint32_t fd,
                             uint32_t handle);

struct ibv_mr *ibv_import_mr(struct ibv_context *context,
                             uint32_t fd,
                             uint32_t handle);

uint32_t ibv_context_to_fd(struct ibv_context *context);

uint32_t ibv_pd_to_handle(struct ibv_pd *pd);

uint32_t ibv_mr_to_handle(struct ibv_mr *mr);

```

# DESCRIPTION

**ibv_export_to_fd**() exports ib hw object (pd, mr,...) from one context to another context. The destination context's file descriptor then can be shared with the other processes by sending it on SCM_RIGHTS socket. Once shared, the destination process can import the exported objects from the shared file descriptor to it's current context by using the equivalent ibv_import_x (e.g. ibv_import_pd) verb. The destruction of the imported object is done by using the ib hw object destroy verb (e.g. ibv_dealloc_pd). The destruction of the kernel object is done when all reference to it are destroyed.

## To export object (e.g. pd), the below steps should be taken:

1. Allocate new shared context (ibv_open_device).
2. Get the new context file descriptor (ibv_context_to_fd).
3. Get the ib hw object handle (e.g. ibv_pd_to_handle).
4. Export the ib hw object to the file descriptor (ibv_export_to_fd).

**ibv_import_pd**(), **ibv_import_mr**() import pd/mr previously exported via export context.

**ibv_context_to_fd**() returs the file descriptor of the given context.

**ibv_pd_to_handle**(), **ibv_mr_to_handle**() returns the ib hw object handle from the given object.

# ARGUMENTS

**ibv_export_to_fd**()

*fd* is the destination context's file descriptor.

*new_handle* is the handle of the new object.

*context* is the context to export the object from.

*type* is the type of the object being exported (e.g. UVERBS_OBJECT_PD).

*handle* is the handle of the object being exported.

**ibv_import_pd**(), **ibv_import_mr**()

*context* the context to import the ib hw object to.

*fd* is the source context's file descriptor.

*handle* the handle of the exported object in the export context as returned from *ibv_export_to_fd*().

**ibv_context_to_fd**()

*context* context obtained from **ibv_open_device**() verb.

**ibv_pd_to_handle**(), **ibv_mr_to_handle**()

*pd*, *mr* obtained from **ibv_alloc_pd**(), **ibv_reg_mr**() verbs.

# RETURN VALUE

**ibv_export_to_fd**() returns 0 on success, or the value of errno on failure (which indicates the failure reason).

**ibv_import_pd**(), **ibv_import_mr**() - returns a pointer to the imported ib hw object, or NULL if the request fails.

**ibv_context_to_fd**() returs the file descriptor of the given context.

**ibv_pd_to_handle**(), **ibv_mr_to_handle**() returns the ib hw object handle from the given object

# SEE ALSO

**ibv_dealloc_pd**(3), **ibv_dereg_mr**(3)

# AUTHORS

Shamir Rabinovitch <shamir.rabinovitch@oracle.com>
Yuval Shaia <yuval.shaia@oracle.com>
